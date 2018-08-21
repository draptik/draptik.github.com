---
layout: post
title: "IntelliJ and Gnome keyboard shortcut conflict: Ctrl Alt s"
date: 2018-08-21 23:01:12 +0000
comments: true
categories: [jetbrains, rider, keyboard-shortcuts, keyboard-bindings, gnome, linux, arch, shortcuts, gnome-extensions]
---
I am currenty experimenting with [Jetbrains Rider](https://www.jetbrains.com/rider/) under Linux. Sticking to the default window manager GNOME for my linux distro (Arch Linux), I ran into some problems with conflicting keyboard shortcuts. Even though Jetbrains' IDEs come with a keyboard setting named `Default for GNOME`...

Starting with the most obvious: I was not able to open Rider's settings using the keyboard shortcut `Ctrl Alt s`.

## `Ctrl Alt s`

This command opens the settings in most IntelliJ products (File -> Settings).

Pressing `Crtl Alt s` "rolled up the window": The current window was minimized to the title bar (an effect I have never seen before and don't need). Double clicking the window title bar expanded the window again. So obviously the keyboard shortcut was already in use. The question being: by which application?

Gnome settings did not reveal any conflicting bindings in the keyboard section!

After some searching the gnome extension `screenshot-window-sizer` turned out being the culprit. I don't know if I installed this extension on purpose or if it was installed as a dependency by some other package or if belongs to gnome's default. The following solution keeps the extension installed: Only the keyboard shortcut is disabled.

Full name of the extension:

`org.gnome.shell.extensions.screenshot-window-sizer`

Here is a short summary how to figure out if your environment is affected by this extension:
``` sh
# Find out if screenshot extension is installed
gsettings list-schemas | grep screenshot-window-sizer

# List keys
gsettings list-keys org.gnome.shell.extensions.screenshot-window-sizer 
cycle-screenshot-sizes-backward
cycle-screenshot-sizes

## Show key value
gsettings get org.gnome.shell.extensions.screenshot-window-sizer cycle-screenshot-sizes
['<Alt><Control>s']

## Show key value
gsettings get org.gnome.shell.extensions.screenshot-window-sizer cycle-screenshot-sizes-backward
['<Shift><Alt><Control>s']
```

The following snippet disables the hijacked `Ctrl Alt s` binding from `screenshot-window-sizer` (found [here](https://extensions.gnome.org/extension/881/screenshot-window-sizer/))
``` sh
# disable:
gsettings set org.gnome.shell.extensions.screenshot-window-sizer cycle-screenshot-sizes []
gsettings reset org.gnome.desktop.wm.keybindings toggle-shaded

# To re-enable: 
gsettings reset org.gnome.shell.extensions.screenshot-window-sizer cycle-screenshot-sizes
gsettings set org.gnome.desktop.wm.keybindings toggle-shaded []
```

Now the keybinding `Ctrl Alt s` works as expected in IntelliJ products -- even with Gnome.
