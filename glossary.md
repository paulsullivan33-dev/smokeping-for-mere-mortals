# Glossary

The terms, collected in one place. Each
is defined the way this guide uses it.

**Alert** — a named pattern over recent
rounds, defined in the Alerts section;
when a target's history matches, mail
is sent.

**CGI** — the old, simple contract by
which a web server runs a program to
make a page. `smokeping.cgi` is the
program that draws SmokePing's pages.

**Daemon** — a program that runs in the
background, working without being
watched. SmokePing's daemon measures,
stores, and checks alerts.

**Edge trigger** — an alert setting:
mail when the pattern *starts* and
*stops* matching, not on every round in
between.

**fping** — the program the FPing probe
drives; it pings many targets, several
times each, and reports every time.

**ICMP** — the Internet Control Message
Protocol. Ping is its echo request and
echo reply — the network's own "are you
there?".

**Jitter** — the variation in round-trip
times from one ping to the next. The
smoke on the graph is jitter, drawn.

**Latency** — how long a round trip
takes. The only speed SmokePing
measures.

**Loss** — the share of a round's pings
that never came back, as a count in the
data and a percentage in patterns.

**Master / slave** — an optional
SmokePing arrangement: slaves measure
from other machines and report to one
master, adding viewpoints.

**Median** — the middle value of a
round's times when sorted. One freak
reply cannot move it; that robustness
is why SmokePing leads with it.

**NaN** — "not a number." In an RRD file
it means *no measurement exists for this
interval* — a gap, not a zero.

**Ping** — one ICMP echo request and
its timed reply; also the command that
sends a few and forgets them.

**Probe** — the part of SmokePing that
knows how to perform one kind of test:
FPing pings, Curl fetches, DNS asks.

**Round** — one test of one target:
`pings` attempts, their times, a median,
and a loss count. The atom of all
graphs and alerts.

**RRD / RRDtool** — round-robin
database: a fixed-size file for
measurements over time, keeping recent
rounds in detail and summarising older
ones. RRDtool is the program and library
that manages them and draws the graphs.

**Smoke** — the grey spread drawn around
a graph's median line: every individual
ping time of each round, plotted. It is
this tool's signature and its name.

**Step** — the seconds between rounds
for a target: 300 by default, fixed when
its RRD file is created.

**Target** — one thing being watched: a
host, a service, a URL. Targets form a
tree in the configuration and the menu
on the web page.
