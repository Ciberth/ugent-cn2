# Chapter 6 How User Space Starts

User space starts in this order:
1. Init
2. Essential low-level services such as `udevd` and `syslogd`
3. Network configuration
4. Mid- and high-level services (cron, printing...)
5. Loging prompts, GUIs, and other high-level applications.

## Introduction to init
_(6.1)_

The init program is a user-space program found in /sbin. Its main purpose is to start and stop the essential service processes on the system. Newer versions have more responsibilities though. There are three major implementations of init in Linux distributions: **System V init, systemd** and **Upstart**.

The main difference between systemd and Upstart is that systemd is goal oriented while Upstart is reactionary (reacting on events).

## System V Runlevels
_(6.2)_

The state of the machine is caled its _runlevel_ (a number from 0 through 6). You can check your system's runlevel with `who -r`. 2 through 4 is text console and 5 means GUI login. 1 is used for single-user mode, 0 for Halt/shutdown and 6 to reboot the system. These numbers may vary depending on Linux distributions.

You can find out what your init type is by looking at config files. `/usr/lib/systemd` and `/etc/systemd` show systemd. `/etc/init` with several _.conf_ files point towards Upstart and an `/etc/inittab` refers to System V.

## Systemd
_(6.4)_

 > Inspired by Apple's launchd. 

When systemd runs at boot time, the following things happens:
1. systemd loads its configuration
2. systemd determines its boot goal, which is usually named default.target
3. systemd determines all of its dependencies of the default boot goal
4. systemd activates the dependencies and the boot goal
5. after the boot, systemd can react to system events

### Units and Unit Types

Since systemd does a lot of things besides operating processes and services (such as mounting filesystems, monitoring network sockets...) there exists this term called a _unit type_. A _unit_ is a specific capability which you can turn on (activating it).

Examples are **service units, mount units, target units**. With `systemctl dot` you can see a dependency tree diagram. 


### Systemd Dependencies

Some basic types are:
* **Requires:** these are strict dependencies
* **Wants:** dependencies for activation only
* **Requisite:** units that must already be active
* **Conflicts:** negative dependencies

> Note: Attach dependencies "in reverse" such as WantedBy.

TODO summary table

You can view a unit's dependencies with the `systemctl show -p type unit` with type the type of dependency, such as Wants or Requires.

To activate units in a particular order, you can use dependency modifiers: **Before** and **After**


### Systemd Configuration







## Extra (slides + notities)

TODO