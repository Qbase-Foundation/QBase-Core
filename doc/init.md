Sample init scripts and service configuration for qbased
==========================================================

Sample scripts and configuration files for systemd, Upstart and OpenRC
can be found in the contrib/init folder.

    contrib/init/qbased.service:    systemd service unit configuration
    contrib/init/qbased.openrc:     OpenRC compatible SysV style init script
    contrib/init/qbased.openrcconf: OpenRC conf.d file
    contrib/init/qbased.conf:       Upstart service configuration file
    contrib/init/qbased.init:       CentOS compatible SysV style init script

1. Service User
---------------------------------

All three Linux startup configurations assume the existence of a "qbasecore" user
and group.  They must be created before attempting to use these scripts.
The OS X configuration assumes qbased will be set up for the current user.

2. Configuration
---------------------------------

At a bare minimum, qbased requires that the rpcpassword setting be set
when running as a daemon.  If the configuration file does not exist or this
setting is not set, qbased will shutdown promptly after startup.

This password does not have to be remembered or typed as it is mostly used
as a fixed token that qbased and client programs read from the configuration
file, however it is recommended that a strong and secure password be used
as this password is security critical to securing the wallet should the
wallet be enabled.

If qbased is run with the "-server" flag (set by default), and no rpcpassword is set,
it will use a special cookie file for authentication. The cookie is generated with random
content when the daemon starts, and deleted when it exits. Read access to this file
controls who can access it through RPC.

By default the cookie is stored in the data directory, but it's location can be overridden
with the option '-rpccookiefile'.

This allows for running qbased without having to do any manual configuration.

`conf`, `pid`, and `wallet` accept relative paths which are interpreted as
relative to the data directory. `wallet` *only* supports relative paths.

For an example configuration file that describes the configuration settings,
see `contrib/debian/examples/qbase.conf`.

3. Paths
---------------------------------

3a) Linux

All three configurations assume several paths that might need to be adjusted.

Binary:              `/usr/bin/qbased`  
Configuration file:  `/etc/qbasecore/qbase.conf`  
Data directory:      `/var/lib/qbased`  
PID file:            `/var/run/qbased/qbased.pid` (OpenRC and Upstart) or `/var/lib/qbased/qbased.pid` (systemd)  
Lock file:           `/var/lock/subsys/qbased` (CentOS)  

The configuration file, PID directory (if applicable) and data directory
should all be owned by the qbasecore user and group.  It is advised for security
reasons to make the configuration file and data directory only readable by the
qbasecore user and group.  Access to qbase-cli and other qbased rpc clients
can then be controlled by group membership.

3b) Mac OS X

Binary:              `/usr/local/bin/qbased`  
Configuration file:  `~/Library/Application Support/QbaseCore/qbase.conf`  
Data directory:      `~/Library/Application Support/QbaseCore`
Lock file:           `~/Library/Application Support/QbaseCore/.lock`

4. Installing Service Configuration
-----------------------------------

4a) systemd

Installing this .service file consists of just copying it to
/usr/lib/systemd/system directory, followed by the command
`systemctl daemon-reload` in order to update running systemd configuration.

To test, run `systemctl start qbased` and to enable for system startup run
`systemctl enable qbased`

4b) OpenRC

Rename qbased.openrc to qbased and drop it in /etc/init.d.  Double
check ownership and permissions and make it executable.  Test it with
`/etc/init.d/qbased start` and configure it to run on startup with
`rc-update add qbased`

4c) Upstart (for Debian/Ubuntu based distributions)

Drop qbased.conf in /etc/init.  Test by running `service qbased start`
it will automatically start on reboot.

NOTE: This script is incompatible with CentOS 5 and Amazon Linux 2014 as they
use old versions of Upstart and do not supply the start-stop-daemon utility.

4d) CentOS

Copy qbased.init to /etc/init.d/qbased. Test by running `service qbased start`.

Using this script, you can adjust the path and flags to the qbased program by
setting the QBASED and FLAGS environment variables in the file
/etc/sysconfig/qbased. You can also use the DAEMONOPTS environment variable here.

4e) Mac OS X

Copy org.qbase.qbased.plist into ~/Library/LaunchAgents. Load the launch agent by
running `launchctl load ~/Library/LaunchAgents/org.qbase.qbased.plist`.

This Launch Agent will cause qbased to start whenever the user logs in.

NOTE: This approach is intended for those wanting to run qbased as the current user.
You will need to modify org.qbase.qbased.plist if you intend to use it as a
Launch Daemon with a dedicated qbasecore user.

5. Auto-respawn
-----------------------------------

Auto respawning is currently only configured for Upstart and systemd.
Reasonable defaults have been chosen but YMMV.
