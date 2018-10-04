
Debian
====================
This directory contains files used to package qbased/qbase-qt
for Debian-based Linux systems. If you compile qbased/qbase-qt yourself, there are some useful files here.

## qbase: URI support ##


qbase-qt.desktop  (Gnome / Open Desktop)
To install:

	sudo desktop-file-install qbase-qt.desktop
	sudo update-desktop-database

If you build yourself, you will either need to modify the paths in
the .desktop file or copy or symlink your qbase-qt binary to `/usr/bin`
and the `../../share/pixmaps/qbase128.png` to `/usr/share/pixmaps`

qbase-qt.protocol (KDE)

