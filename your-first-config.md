# Your first config

**Outcome: you have a complete
configuration that watches two targets —
your router and a public DNS server —
and SmokePing itself has told you the
file is valid.**

Check: run the checker against your
file:

```sh
sudo smokeping --check --config=/etc/smokeping/config
```

You want the answer the lab got, twice,
for both of its configurations
(transcript t04):

```text
Configuration file '...' syntax OK.
```

Anything else is an error message naming
the line — fix the line, not the whole
file, and run the check again.

## One decision before you write anything

The **Database** section holds `step` —
the seconds between rounds — and it
cannot be changed later without throwing
away the data files it shapes. The
default is 300 (five minutes), and it is
the right choice for real use: fine
enough to catch an evening's bad patch,
coarse enough to keep for years. The
lab used 30, purely so a short session
would contain several rounds; its
configuration says so in a comment, and
so should yours if you ever do the same.
Decide first, then write.

## The configuration, section by section

A minimal but complete file looks like
this. Paths are the Ubuntu install's;
yours may already have a General section
you only need to personalise.

```text
*** General ***
owner    = Your Name
contact  = you@example.com
mailhost = localhost
sendmail = /usr/sbin/sendmail
imgcache = /var/cache/smokeping/images
imgurl   = ../smokeping/images
datadir  = /var/lib/smokeping
piddir   = /run/smokeping
cgiurl   = http://localhost/smokeping.cgi
smokemail = /etc/smokeping/smokemail
tmail    = /etc/smokeping/tmail

*** Alerts ***
to = you@example.com
from = smokeping@example.com

+bigloss
type = loss
pattern = ==0%,==0%,==0%,>20%
comment = No loss for three rounds, then real loss
edgetrigger = yes

*** Database ***
step     = 300
pings    = 20

AVERAGE  0.5   1  28800
AVERAGE  0.5  12   9600
    MIN  0.5  12   9600
    MAX  0.5  12   9600
AVERAGE  0.5 144   2400
    MAX  0.5 144   2400
    MIN  0.5 144   2400

*** Presentation ***
template = /etc/smokeping/basepage.html
charset = utf-8

+ overview
width = 600
height = 50
range = 10h

+ detail
width = 600
height = 200
unison_tolerance = 2

"Last 3 Hours"    3h
"Last 30 Hours"   30h
"Last 10 Days"    10d
"Last 360 Days"   360d

*** Probes ***
+ FPing
binary = /usr/bin/fping

*** Targets ***
probe = FPing
menu = Top
title = Network latency
remark = Latency at home, measured from this machine.

+ Home
menu = Home
title = Home targets

++ Router
menu = Router
title = The router, the first hop out
host = 192.168.1.1
alerts = bigloss

++ PublicDNS
menu = Public DNS
title = A public DNS server, out on the internet
host = 1.1.1.1
alerts = bigloss
```

## Reading what you just wrote

- **Targets is a tree.** `+ Home` is a
  branch, `++ Router` a leaf under it.
  The tree is the menu on the web page,
  and each leaf becomes one RRD file:
  here, `Home/Router.rrd` and
  `Home/PublicDNS.rrd` in the data
  directory.
- **The two targets are a diagnosis
  pair.** If the router stays clean
  while the DNS server smokes, the
  trouble is beyond your house. If both
  smoke together, it starts at home.
  Chapter 10 builds a whole recipe on
  this pair.
- **`host` is all a target strictly
  needs** — an address or a name. Home
  routers are very often 192.168.1.1; if
  yours is not, its address is the
  "default gateway" your computer
  already knows (`ip route` shows it).
- **The Alerts lines earn their keep
  later** (chapter 9), but they must be
  defined before a target may name them,
  and `--check` compiles the patterns —
  a pattern typo fails here, in the
  checker, not during the night it was
  meant to catch.
- **Twenty pings per round** (`pings =
  20`) is what makes the smoke in
  chapter 7 so informative: twenty
  samples spread out enough to show a
  shape. Five is the floor worth using.

## The split-file variant

Ubuntu's stock configuration keeps each
section in its own file under
`/etc/smokeping/config.d/` and includes
them from `/etc/smokeping/config`. That
is the same configuration wearing a
different coat — edit the section files
and run the same `--check`. This guide
shows the single-file form because it is
easier to read whole, and the lab's two
kept configurations (`smokeping-lab/config`
and `config.fping`) use it.
