---
layout: post
title: 'Arch Linux: gnome-terminal new tmux session option'
---

Copy the `.desktop` file to your local applications direcory:

    $ cp /usr/share/applications/org.gnome.Terminal.desktop ~/.local/share/applications/

Edit `~/.local/share/applications/org.gnome.Terminal.desktop`

Add `new-tmux-session` to `Actions`:

    Actions=new-window;preferences;new-tmux-session;

Add the new action:

    [Desktop Action new-tmux-session]
    Name=New tmux Session
    Exec=gnome-terminal --command tmux new
