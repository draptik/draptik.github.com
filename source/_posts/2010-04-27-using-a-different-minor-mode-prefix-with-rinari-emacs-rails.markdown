---
author: draptik
comments: true
date: 2010-04-27 20:40:27
layout: post
slug: using-a-different-minor-mode-prefix-with-rinari-emacs-rails
title: Using a different minor-mode-prefix with Rinari (Emacs Rails)
wordpress_id: 311
categories:
- programming
---

[Rinari](http://rinari.rubyforge.org/) is a Ruby on Rails Minor Mode for GNU/Emacs.

**Problem:** Most key mappings / keyboard shortcuts rely on the hard coded strings `;` and `'` (I.e. `C-; f c` -> `rinari-find-controller`). These prefixes are difficult to access on a German keyboard because they require the shift modifier.

**Solution:** The following code adds a comma to the list of rinari-prefix strings. Add it to your GNU/Emacs configuration file (i.e. `~/.emacs`):

``` scheme ~/.emacs
(add-to-list 'load-path "~/your-path-to-rinari-source-code/rinari")
﻿;; This patch must appear before the line "(require 'rinari)"!
(defvar rinari-minor-mode-prefixes
(list ";" "'" ",") ;; CHANGE Added comma to prefix list
     "List of characters, each of which will be bound (with C-c) as a rinari-minor-mode keymap prefix.")

(require 'rinari)
```

**Result: **We can now access the command `rinari-find-controller` with the key sequence `C-c , f c`.

**Side note:** I'm all ears for more Emacs-Lispy solutions to this problem. ;-)
