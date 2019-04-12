# GNOME session save and restore
By Arnon Weinberg, on July 8th, 2013

This script is used to save and restore a desktop session.

The script, just like following description, was taken from Arnon's blog http://blog.thewebsitepeople.org/2013/07/gnome-session-save-and-restore/. 

# Why this script?

Some desktop managers do offer some session management features.
Under GNOME it may be possible to run gnome-session-properties manually and turn on “Automatically remember running applications when logging out”.
However, some GNOME implementations (notably Ubuntu’s) appear to be deemphasizing or deprecating this feature in favour of the Suspend or Hibernate feature.

Both the automated session save and suspend feature however are limited to saving/restoring sessions only on clean logout/login.

I use this script because sometimes I like to restore my session to a previous state, and sometimes I just need to reboot.

# How to use

Download the script, place it on your PATH, and be sure to give it executable permissions.

To save the current session:
>session save

To restore the saved session:
>session restore

# Dependencies

This script uses Perl, xprop, wmctrl, and optionally xdotool.  To ensure that all dependencies exist use:
>yum install perl xorg-x11-utils wmctrl xdotool

I only test on my Fedora and CentOS / GNOME Shell desktops.  Others have reported it working on Ubuntu / Unity.

# Details

The session restore command takes an optional additional “level” argument 0-2.  Two use-cases illustrate its use.

1. Restore window geometries:
– After rearranging the desktop layout with xrandr
– After restarting an application

I have an external monitor plugged into my laptop, and many windows open across multiple workspaces and screens.  At this point I save the session for future reference.  Then I unplug the external monitor to take the laptop on the road, losing window positions.  Later I return and plug the monitor back in.  I run the session restore to reset window geometries as they were.

>session restore 0

Restore existing windows: This is the default.  It scans through the saved window IDs and for each window ID found still running, resets its geometry to the saved values.

>session restore 1

Restore matching windows: This goes one step further.  In case an application was shut down and restarted since the session was saved, the window manager will often give it the same ID as before, but sometimes this is not possible.  In this case, the restore will find a window with similar properties and set its geometry instead.

2. Restore running applications:
– After system reboot:
– Kernel upgrade
– Hardware upgrade
– Non-recoverable software error
– Battery dead
– Power outage
– After Application crash or accidentally closing an application

In this case, the session is shut down for any number of reasons.  Hopefully the session was saved at some point before this – if you always have the same applications running, then saving it once will do; if your session changes all the time, then perhaps add session saving to cron.

>session restore 2

Restore missing application: Applications for which no matching instance is found are restored using their original command-line.

Note: The session script never shuts down applications.
Note: The session is stored in ~/.config/gnome-session/session.ini

# Limitations

There are many currently, but it saves me about 80% of the work of restoring a session manually.

The script contains a list of exceptions that is currently manually maintained. It lists applications that should be handled specially when restoring them (level 2).

Because some applications handle their own session save and restore, but they don’t handle restoring geometries, the session restore process can be a 3-step process sometimes: (1) Run session restore 2 to restart the application, (2) follow the application’s session restore process, and (3) run session restore 1 to restore its geometries.
