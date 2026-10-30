# Installing SmokePing

**Outcome: SmokePing is installed, and
you have proved the program runs by
asking it its own version.**

Check: run `smokeping --version`. You
should get a version string back — the
lab's printed `2.008002`, which is
SmokePing 2.8.2. Any 2.8.x answer means
the program and its Perl libraries are
in place and loadable.

## The normal install

On Ubuntu or Debian, one command brings
SmokePing and everything it needs:

```sh
sudo apt update
sudo apt install smokeping
```

The package pulls in its working parts
as dependencies — this guide's lab took
the same packages apart and can list
them from the evidence: **rrdtool** (the
database and graph tool), **fping** (the
ping program the default probe drives),
and a shelf of Perl libraries including
the RRD bindings. Twenty-eight packages
in all, under four megabytes to download
— SmokePing itself is a set of Perl
programs, and Perl is already on the
machine.

The install lays out the standard map:

- `/usr/sbin/smokeping` — the daemon and
  command-line tool in one
- `/etc/smokeping/config` — the
  configuration, which on Ubuntu is a
  short file that includes the real
  sections from `/etc/smokeping/config.d/`
- `/var/lib/smokeping` — where the RRD
  files will live
- `/usr/lib/cgi-bin/smokeping.cgi` — the
  web program, plus an Apache snippet at
  `/etc/apache2/conf-available/smokeping.conf`
- a `smokeping` service you control with
  `systemctl`, like any other

Start it once the configuration exists —
chapter 4 — with:

```sh
sudo systemctl enable --now smokeping
```

## A note about fping and permission

Pinging needs a kind of network socket
that ordinary users are not normally
allowed to open. The `fping` package
handles this for you on a normal install
(it arrives with the extra permission it
needs, granted by the package). You only
meet this machinery when it is missing —
a hand-copied `fping`, an unusual
container, a locked-down sandbox like
this guide's lab — and then *every*
target looks dead. Chapter 6 shows what
that looks like in the data, and chapter
13 gives the checks. For now, remember
the shape of the trap: it is the one
failure that disguises itself as the
network's fault.

## How the lab installed it

This guide's lab machine is a sandbox
with no package-manager session, so the
lab fetched the same Ubuntu 24.04
packages the command above installs,
unpacked them into a folder without
installing, and ran the programs from
there. Same bytes, same versions, no
steps skipped: `smokeping --version`,
`rrdtool --version` and `fping -v`
answered 2.008002, 1.7.2 and 5.1 from
the unpacked tree (transcript t02 in the
lab folder). On your machine the plain
`apt install` above is the right route,
and everything from chapter 4 on applies
unchanged.
