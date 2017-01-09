---
layout: post
title: "Bash tricks"
date: 2017-01-10 00:22:02 +0100
comments: true
categories: [bash, linux]
---
Over the holidays I stumbled across 2 neat bash tricks to simplify navigation between folders:

- `autocd`
- `autojump`

Neither of these features is new.

## autocd

`autocd` is very simple: It's a built-in bash feature which prepends `cd` in case you enter a valid path.

```bash
$ cd /etc
$ /etc # the same
```

### Setup

Add this to your `~/.bashrc`:
```bash
shopt -s autocd
```

## autojump

> A cd command that learns - easily navigate directories from the command line

Directly from the source at [autojump](https://github.com/wting/autojump):

Usage:

`j` is a convenience wrapper function around `autojump`. Any option that can be used with `autojump` can be used with `j` and vice versa.

Jump To A Directory That Contains `foo`:

```bash
j foo
```

Jump To A Child Directory:

Sometimes it's convenient to jump to a child directory (sub-directory of current directory) rather than typing out the full name.
```bash
jc bar
```
Open File Manager To Directories (instead of jumping):

Instead of jumping to a directory, you can open a file explorer window (Mac Finder, Windows Explorer, GNOME Nautilus, etc.) to the directory instead.
```bash
jo music
```
Opening a file manager to a child directory is also supported:
```bash
jco images
```
Using Multiple Arguments:

Let's assume the following database:
```bash
30   /home/user/mail/inbox
10   /home/user/work/inbox
```
`j` in would jump into `/home/user/mail/inbox` as the higher weighted entry. However you can pass multiple arguments to `autojump` to prefer a different entry. In the above example, `j w in` would then change directory to `/home/user/work/inbox`.

For more options refer to help:
```bash
autojump --help
```
