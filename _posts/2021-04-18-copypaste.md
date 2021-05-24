---
layout: post
title:  "Getting copy/paste to work"
date:   2021-04-18 01:06:15 -0400
published: true
---

Copy/paste between Firefox and terminal wasn't working
(I run Archlinux with i3wm).
Here's my workaround.

Install `xclip`, which manages 3 clipboards:
- primary; for things being highlighted. Contents of the primary clipboard are
	ephemeral, and get overwritten when something else is highlighted.
	Moreover, it is asynchronous, so if the program in which the text is copied
	from is terminated, the selection disappears.
- secondary: mysterious
- clipboard: from which regular `ctrl+v` is pasted from.


So we bind `<F8>` to copy contents of primary to clipboard.
First copy the default configuration for `xbindkeys` to `~/.xbindkeysrc`:

	$ xbindkeys -d > ~/.xbindkeysrc

Then add the following:

	"xclip -o -selection primary | xclip -i -selection clipboard"
		F8

Load the file with

	$ xbindkeys

or add it to `~.xinitrc`.

After highlighting something, say in visual mode in Vim,
`<F8>` acts as a sort of "copy" button.
(I would use `ctrl+shift+c`, but Firefox already has that shortcut taken.)

Also, pasting into terminal doesn't work great,
so we assign `<F9>` to pasting,
by adding the following to `~/.Xresources`:

	*VT100*translations: #override \n\
		<Key>F9:    insert-selection(PRIMARY,CLIPBOARD)

