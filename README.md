# SmokePing for Mere Mortals

Every network argument starts the same way:
"it was slow last night." Slow when? Slow
for how long? Slow to where? A single
**ping** answers for one second, and then
the evidence is gone. **SmokePing** is the
program that keeps the evidence. It tests
the machines and services you name, round
after round, day after day, stores every
result, and draws the results as graphs
with a feature no other common tool has:
**smoke**. Each round of pings leaves not
just a line for its middle value but a grey
smudge showing how spread out the replies
were — so a connection that is fast but
jittery looks different from one that is
slow but steady, because they *are*
different, and they feel different to use.

This guide explains how SmokePing is put
together, how to install it, how to write
a configuration that watches the things
you care about, and how to read what it
draws. Then it does something most
monitoring guides skip: it **proves**
the numbers underneath the graph, by
opening the data files and reading the
measurements directly.

The lab that wrote this guide ran the
real thing: **SmokePing 2.8.2**, with
RRDtool 1.7.2 and fping 5.1, from the
ordinary Ubuntu 24.04 packages. It wrote
two configurations, checked both, ran
the daemon for eleven rounds, and read
the stored measurements back out of the
database files. It also ran SmokePing's
own web program and kept the graph it
drew.

What the lab could not do, it did not
fake. The lab machine lives in a sandbox
that forbids the raw network sockets
pinging needs — not just to the internet,
but to the machine itself, and not just
for ordinary users, but for root. So the
**FPing** probe, which this guide teaches
as the normal way to watch a network,
passed every check and then honestly
failed to send a single ping, and this
guide shows you that failure and how to
recognise it. The measurements that fill
the lab's database and its graph were
made by SmokePing's **Curl** probe
fetching a page from a web server on the
same machine — real timings of real
fetches, standing in for ping times
because in this sandbox they are what
can be measured. On your machine, which
is not in this sandbox, the FPing setup
in chapter 4 is the one to run.
`criteria.md` keeps the full ledger of
what was run and what was only read.

One rule belongs here, before any of it:
SmokePing measures by actively testing.
Point it at machines and networks you own,
or that you have permission to test.
Never at strangers.

## What this guide covers

- [**What SmokePing is**](what-smokeping-is.md) —
  a latency historian: why a film of your
  network beats a snapshot, and the rule
  about whose machines you may test
- [**The moving parts**](the-moving-parts.md) —
  the daemon, the probes, the round-robin
  database, the configuration file, the
  web page, and the alerts — and which
  part holds what
- [**Installing SmokePing**](installing-smokeping.md) —
  the package install, what arrives with
  it, and the version check that proves
  it runs
- [**Your first config**](your-first-config.md) —
  the six sections of the configuration
  file, a working example that watches
  your router and a public DNS server,
  and the check that validates it
- [**The first graph**](the-first-graph.md) —
  starting the daemon, waiting one round,
  and opening the web page SmokePing
  serves
- [**Proving the numbers**](proving-the-numbers.md) —
  opening the data files with RRDtool and
  reading the measurements directly,
  including how a broken probe can look
  exactly like a dead machine
- [**Reading the smoke**](reading-the-smoke.md) —
  the median line, the smoke around it,
  the loss colours, and the statistics
  under every graph
- [**Choosing targets and probes**](choosing-targets-and-probes.md) —
  organising what you watch, FPing and
  its variants, and the other probes the
  program ships — DNS, HTTP, SSH, and more
- [**Alerts**](alerts.md) — patterns that
  describe a bad patch in words, checked
  before they can fire, and mail when
  they do
- [**Recipes**](recipes.md) — the
  evidence kit for an argument with your
  provider, wired against wireless, and
  watching a website the way the lab did
- [**The neighbours**](the-neighbours.md) —
  Prometheus and Grafana, Nagios and its
  family, Uptime Kuma and the rest: what
  each does that SmokePing doesn't, and
  vice versa
- [**Limits**](limits.md) — latency and
  nothing else, the view from one chair,
  and detail that fades as it ages
- [**Troubleshooting**](troubleshooting.md) —
  the configuration error, the probe that
  cannot run, the empty graph, and the
  other traps this guide's lab fell into
  or read about
- [**Glossary**](glossary.md) — the
  terms, collected in one place

## What it doesn't cover

Bandwidth and throughput testing —
SmokePing measures how *long* things
take, never how *much* fits. Finding out
*why* a path is slow is also out: the
graph proves the slowdown happened, and
roughly where it started, but the cause
is yours to hunt. Watching from many
places at once (SmokePing's master and
slave mode) is described in outline only;
the lab ran one machine. And the web
server side is covered as far as the
package's own Apache snippet — the lab
ran SmokePing's web program directly,
not behind a full web server.

## How to use this guide

Read chapter 1 for the idea, and chapter
2 before installing anything — knowing
which part holds what turns later
confusion into a named problem. Chapters
3 and 4 get you an installed program and
a checked configuration; chapter 5 shows
your first graph in the same sitting.
Chapter 6 is the one to read slowly even
if your graphs look fine: its checks are
how you catch a probe that has stopped
measuring and started merely reporting.
The lab's transcripts live in
`smokeping-lab/transcripts/`, its two
configurations are `smokeping-lab/config`
and `smokeping-lab/config.fping`, and its
kept graph is `smokeping-lab/graph-localweb.png`.

---

## Part of the series

This guide is one of the [For Mere Mortals](https://paulsullivan33-dev.github.io/mere-mortals/) guides: plain-language guides to the tools programmers and makers use.

Related guides:

- [Prometheus and Grafana for Mere Mortals](https://paulsullivan33-dev.github.io/prometheus-and-grafana-for-mere-mortals/)
- [Networking for Mere Mortals](https://paulsullivan33-dev.github.io/networking-for-mere-mortals/)
- [Beszel for Mere Mortals](https://paulsullivan33-dev.github.io/beszel-for-mere-mortals/)

## License

The writing in this guide is licensed under [CC BY 4.0](https://github.com/paulsullivan33-dev/smokeping-for-mere-mortals/blob/main/LICENSE). Code examples and lab scripts are licensed under the [MIT License](https://github.com/paulsullivan33-dev/smokeping-for-mere-mortals/blob/main/LICENSE-CODE).
