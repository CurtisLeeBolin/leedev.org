---
layout: post
title: 'Gnome 3.20: Minimal Title Bar Size'
date: '2016-05-09 15:38:52'
---

This uses Gnome's `CSS` theming `API`. The `API` changed for Gnome 3.20.

Create and edit this style sheet.
`~/.config/gtk-3.0/gtk.css`

```
headerbar.default-decoration {
  padding-top: 0px;
  padding-bottom: 0px;
  min-height: 0px;
  font-size: 0.8em;
}

headerbar.default-decoration button.titlebutton {
  padding: 0px;
  min-height: 0px;
}
```

Make `gnome-terminal` use the normal headerbar

```
$ gsettings set org.gnome.Terminal.Legacy.Settings headerbar false
```

To see the results immediately, you can restart the gnome shell.
Press `Alt` + `F2`.
Type `r` and press `enter`.
