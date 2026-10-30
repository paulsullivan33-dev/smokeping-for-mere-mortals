# The moving parts

**Outcome: you can name SmokePing's six
parts and say which one holds what — so
when something is wrong, you know which
part to look at.**

Check: someone says "the graph is empty
but the daemon is running." After this
chapter you should be able to name the
two parts you would inspect first — the
probe (is it measuring?) and the
database (is anything being stored?) —
before touching the web page.

SmokePing has six parts. Five are
programs or files you will meet by name;
one is an idea — the round — that ties
them together.

## The daemon

`smokeping` itself is a long-running
program, the daemon. It reads the
configuration, starts one worker per
probe, and keeps a steady rhythm: every
**step** (five minutes by default) each
target gets tested once. It writes the
results away and checks the alert
patterns. When you change the
configuration, the daemon is the part
you restart. If it is not running,
nothing is measured — the web page will
happily keep showing you old graphs,
which is the first trap in chapter 13.

## The probes

A **probe** is the part that knows how
to perform one kind of test. The default
probe, **FPing**, runs the `fping`
program, which sends ICMP echo requests
— pings — to a target and times each
reply. Other probes fetch web pages,
ask DNS servers questions, open SSH
connections, and more; the probe list
that ships with the program is long, and
chapter 8 walks through it. Each probe
runs as its own process, so a slow probe
cannot delay the others. The important
consequence: *SmokePing can only report
what its probe succeeded in doing.* If
the probe cannot run — wrong permissions,
missing program — SmokePing records that
round as total loss, which looks exactly
like a dead target. Chapter 6 shows a
real case.

## The round

One test of one target is a **round**.
In a round the probe makes several
attempts — five in this guide's lab,
twenty in a default setup — and collects
each attempt's time. From those times
SmokePing keeps the **median** (the
middle value when sorted), the loss
count (how many attempts got no answer),
and every individual time, because the
spread of those times becomes the smoke
on the graph. A round is the atom of
everything that follows: graphs summarise
rounds, alerts pattern-match over
rounds, and the database stores one row
per round.

## The database

Results live in **RRD files**, one per
target, managed by a library called
RRDtool. RRD stands for round-robin
database: it is built for measurements,
keeps fine detail for recent rounds, and
automatically summarises older rounds
into coarser averages so the file never
grows. That is why a year of history
fits in a fixed few hundred kilobytes
per target — and also why last year's
graph is smoother than last hour's,
which chapter 12 returns to. The files
are not secret: chapter 6 opens one
with `rrdtool` and reads the numbers
directly. If you ever doubt the graph,
the database is where you check.

## The configuration file

Everything SmokePing does is described
in one text file, `/etc/smokeping/config`
on a normal install. It has six sections:
**General** (who runs this, where files
live), **Alerts**, **Database** (the step
and how many pings per round),
**Presentation** (how the web page
looks), **Probes** (which tests exist),
and **Targets** (what to test — the only
section you will edit often). Chapter 4
builds one piece by piece. The daemon
reads it once at startup; the web
program reads it on every visit.

## The web program

The page you look at is drawn by
`smokeping.cgi`, a CGI program usually
run by a web server such as Apache.
There is no separate web application
and no second database: the CGI reads
the same configuration and the same RRD
files, and draws each graph at the
moment you ask, using RRDtool. It also
keeps a small cache of drawn images so
repeat visits are cheap. Chapter 5 gets
it on screen.

Hold the map in mind — daemon measures,
probes test, rounds accumulate, RRD
files remember, the CGI draws — and the
rest of this guide is just detail.
