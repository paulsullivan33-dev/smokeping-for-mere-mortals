# The neighbours

**Outcome: you can say what the tools
around SmokePing do better, what
SmokePing does that they don't, and when
running two of them is sensible instead
of redundant.**

Check: for each neighbour below, finish
the sentence "if I already had this, I
would still want SmokePing when …". If
you cannot, you may genuinely not need
SmokePing — that is a fine conclusion.

These tools are characterised from what
they are, not benchmarked; only
SmokePing was run in this guide's lab.

## Prometheus and Grafana

The heavyweight neighbour, and the one
this series covers in its own guide.
Prometheus collects *every* kind of
number — CPU, memory, request rates,
queue depths, including from the
machines themselves — and Grafana draws
dashboards over all of it. Its companion
Blackbox exporter can ping and fetch
URLs, so it *can* watch latency. What it
does not draw is the smoke: each round
becomes summary statistics, and the
per-ping spread — the jitter story —
fades into percentiles you must think to
ask for. If your question is "how is
everything?", Prometheus is the answer.
If your question is "*when* was the
network bad, and in what way?", people
still reach for SmokePing's graphs.

## Nagios, Icinga, Zabbix

The alerting-first family. These watch
services and machines, decide up or
down, and escalate — pages, rosters,
dependencies ("don't alert for the whole
office when the router is the fault").
SmokePing's alerting is a side door by
comparison: patterns and email. But
their latency history is usually a
single graph line of averages, with the
per-round distribution nowhere. Teams
run both: Nagios-family for "wake me
up," SmokePing for "show the court what
happened." At home scale, SmokePing's
alerts are often enough; at company
scale, the neighbour earns its keep.

## Uptime Kuma

The up/down specialist: a friendly page
of green and red — is the service
answering at all? It answers its one
question beautifully and keeps response
times as a simple line. It will not show
you jitter, loss colours, or a ladder of
targets along a path. If "is it up?" is
the whole question, it is the lighter
tool. The moment the question becomes
"it is up, but *bad*", you are back here.

## Beszel, and the machine watchers

Beszel (also in this series) watches
your *machines* — processor, memory,
disk, containers — from the inside.
SmokePing watches the *paths between*
them, from the outside, by timing
replies. They overlap almost nowhere
and excuse each other constantly: Beszel
says the server was idle and healthy
while SmokePing's graph to it smoked —
that pair of facts points at the network
between them, and neither tool alone
could have said so.

## The snapshot tools: ping and mtr

`ping` you know — a moment's answer,
gone when the terminal scrolls. `mtr`
combines ping with a route trace: it
shows each hop along the path and its
timings, live. It is the tool to reach
for *during* an incident, when SmokePing's
ladder has told you the trouble is
mid-path and you want hop names. But
`mtr` only reports while you watch.
SmokePing is what was watching at 3 a.m.
Use mtr to investigate the present;
SmokePing to prove the past.
