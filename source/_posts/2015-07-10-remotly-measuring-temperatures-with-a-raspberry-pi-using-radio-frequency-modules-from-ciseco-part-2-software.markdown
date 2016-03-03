---
layout: post
title: "Remotly measuring temperatures with a Raspberry Pi using radio frequency modules from Ciseco (Part 2: Software)"
date: 2015-07-10 00:13:12 +0200
comments: true
categories: [Raspberry Pi, IoT]
---
In the [previous post](/blog/2015/07/10/remotly-measuring-temperatures-with-a-raspberry-pi-using-radio-frequency-modules-from-ciseco-part-1-hardware/) I described how to setup the hardware for measuring temperatures at home.
This post will use a small python program to save the recorded temperatures to a database.

For simplicity's sake we'll be using SQLite3 as our database.

Aside from our (indoor) temperature sensors, we'll also retrieve outdoor temperatures using the free service [weather underground](http://www.wunderground.com/) (registration required).

Note: In case your Raspberry Pi does not have access to the internet you can just exlude the weather underground parts in the code below.

All following code is intended to run on the Raspberry Pi receiving the data.

### Create database (SQLite3)

The database schema is simple. We need one table to store the temperatures, and another to store sensor information.

Create a new file called `templog.db`:

``` sh
$ touch templog.db
```

There are different ways to interact with SQLite3 (cli (=interactive), script, api).

The goal is to execute the following sql statements:

``` sql
CREATE TABLE sensors
(
    name TEXT NOT NULL,
    id TEXT NOT NULL,
    baudrate INTEGER,
    port TEXT NOT NULL,
    active INTEGER
);
CREATE TABLE temps
(
    timestamp TEXT,
    temp REAL,
    ID TEXT
);
```

Simplest solution is to open the newly created file `templog.db` with `sqlite3` (cli/interactive) ...

```
sqlite3 templog.db
```

...and past the previous code block. Should look like this:

```
$ sqlite3 demo.db 
SQLite version 3.8.10.2 2015-05-20 18:17:19
Enter ".help" for usage hints.
sqlite> CREATE TABLE sensors
   ...> (
   ...>     name TEXT NOT NULL,
   ...>     id TEXT NOT NULL,
   ...>     baudrate INTEGER,
   ...>     port TEXT NOT NULL,
   ...>     active INTEGER
   ...> );
sqlite> CREATE TABLE temps
   ...> (
   ...>     timestamp TEXT,
   ...>     temp REAL,
   ...>     ID TEXT
   ...> );
```

We've created a database.

The important table is `temps`: It will contain the measurements.

The other table (`sensors`) contains informations about the sensors, which are currently only needed for the weather underground 'sensor'. And yes: the column names/types are not optimal.

Note: `timestamp TEXT` will bite us in the ass, but SQLite3 does NOT have any date type.


### Monitor script

I found this nice script somewhere on [Github](https://github.com/kal001/temperature). So Thank You [kal001](https://github.com/kal001)!

Here's my [gist link for the script below](https://gist.github.com/draptik/36834b68b7b4d6366f38).

Place this script side-by-side to `temps.log`.

Save it as `monitor.py`.

You will probably want to modify the values for `dbname`, `TIMEOUT`, `debug.txt`, etc.

``` python
#!/usr/bin/env python

import sqlite3
import threading
from time import time, sleep, gmtime, strftime

import serial
import requests

# global variales

# sqlite database location
dbname = 'templog.db'

# serial device
DEVICE = '/dev/ttyAMA0'
BAUD = 9600

ser = serial.Serial(DEVICE, BAUD)

# timeout in seconds for waiting to read temperature from sensors
TIMEOUT = 30

# weather underground data
WUKEY = ''
STATION = ''
# time between weather underground samples in seconds
SAMPLE = 30 * 60


def log_temperature(temp):
    """
    Store the temperature in the database.
    """
    
    conn = sqlite3.connect(dbname)
    curs = conn.cursor()

    curs.execute("INSERT INTO temps values(datetime('now', 'localtime'), '{0}', '{1}' )".format(temp['temperature'], temp['id']))

    conn.commit()
    conn.close()

    
def get_temp():
    """
    Retrieves the temperature from the sensor.

    Returns -100 on error, or the temperature as a float.
    """

    global ser

    tempvalue = -100
    deviceid = '??'
    voltage = 0

    fim = time() + TIMEOUT

    while (time() < fim) and (tempvalue == -100):
        n = ser.inWaiting()
        if n != 0:
            data = ser.read(n)
            nb_msg = len(data) / 12
            for i in range(0, nb_msg):
                msg = data[i*12:(i+1)*12]
                deviceid = msg[1:3]

                if msg[3:7] == "TMPA":
                    tempvalue = msg[7:]

                if msg[3:7] == "BATT":
                    voltage = msg[7:11]
                    if voltage == "LOW":
                        voltage = 0
        else:
            sleep(5)

    return {'temperature':tempvalue, 'id':deviceid}


def get_temp_wu():
    """
    Retrieves temperature(s) from weather underground (wu) and stores it to the database
    """

    try:
        conn = sqlite3.connect(dbname)
        curs = conn.cursor()
        query = "SELECT baudrate, port, id, active FROM sensors WHERE id like 'W_'"

        curs.execute(query)
        rows = curs.fetchall()

        #print(rows)

        conn.close()

        if rows != None:
            for row in rows[:]:
                WUKEY = row[1]
                STATION = row[0]

                if int(row[3]) > 0:
                    try:
                        url = "http://api.wunderground.com/api/{0}/conditions/q/{1}.json".format(WUKEY, STATION)
                        r = requests.get(url)
                        data = r.json()
                        log_temperature({'temperature': data['current_observation']['temp_c'], 'id': row[2]})
                    except Exception as e:
                        raise
                        
    except Exception as e:
        text_file = open("debug.txt", "a+")
        text_file.write("{0} ERROR:\n{1}\n".format(strftime("%Y-%m-%d %H:%M:%S", gmtime()), str(e)))
        text_file.close()

         
def main():
    """
    Program starts here.
    """
    
    get_temp_wu()
    t = threading.Timer(SAMPLE, get_temp_wu)
    t.start()

    while True:
        temperature = get_temp()

        if temperature['temperature'] != -100:
            log_temperature(temperature)

        if t.is_alive() == False:
            t = threading.Timer(SAMPLE, get_temp_wu)
            t.start()

if __name__ == "__main__":
    main()
```

Run the script. Open another shell, take a look inside the database (new data arriving once an hour?).

Don't forget to start the script after turning off the Raspberry Pi. Or include the script in your boot process (init, systemd).

[Part 3](/blog/2015/07/30/remotly-measuring-temperatures-with-a-raspberry-pi-using-radio-frequency-modules-from-ciseco-part-3-ui/) will provide a UI for the collected data.
