# Choosing targets and probes

**Outcome: you can design a target tree
that answers questions instead of merely
listing machines, and you know which
probe each kind of target needs.**

Check: look at your configuration's
Targets section and name the question
each branch answers. "Home answers
*is it us or them?*" counts. A branch
that answers nothing gets merged or
deleted.

## Design the tree around questions

Targets form a tree because *you* think
in groups: by place (`Home`, `Office`),
by role (`Servers`, `Network`), by
question (`IsItUsOrThem`). The tree is
the web page's menu and the alert
grouping, so design it for the person
reading graphs at midnight — future you.

The single most useful shape is the
**ladder**: targets at increasing
distance along one path.

```text
+ Path
++ Router        host = 192.168.1.1
++ ProviderEdge  host = (your provider's first hop)
++ PublicDNS     host = 1.1.1.1
```

When a bad patch appears, walk the
ladder: the first rung that smokes is
roughly where the trouble lives. Smoke
on every rung points back at your own
machine or its cable. A clean router
with a smoky DNS server clears your
house. Chapter 10's first recipe is
built on exactly this.

Keep the list short enough to stay
honest. Every target is tested every
step, forever; twenty well-chosen
targets beat two hundred forgotten ones,
and a target nobody reads is just
traffic.

## FPing, the default probe

**FPing** is what almost every
SmokePing install uses: ICMP pings, the
`pings` count from your Database
section, one process driving `fping`.
Its close relatives ship alongside it —
the lab counted the probe modules in
the package, and the FPing family is:

- **FPing** — IPv4 pings, the default.
- **FPing6** — the same over IPv6
  (`fping6`-era setups use `binary`
  pointing at an IPv6-capable fping).
- **FPingContinuous** — keeps `fping`
  running and reads its stream, for
  finer-grained loss detection.
- **RemoteFPing** — runs the pinging on
  another machine over SSH and reports
  back: a first taste of watching from
  somewhere else.

## Probes that ask services, not machines

A ping proves a machine's network stack
answered. Sometimes the question is
about a *service*. The package ships
probes for those too — named here from
the module list the lab read in the
installed package (transcript t02's
tree; only FPing and Curl were run in
the lab, so the rest are characterised,
not bench-tested):

- **Curl** — fetches a URL and times
  it. This is the probe the lab actually
  measured with: its target takes a
  `urlformat` such as `http://%host%:8080/`
  in place of ping semantics, and its
  "loss" is failed fetches. The right
  tool for "is the website slow, or is
  the network?"
- **DNS** — times a real DNS lookup
  against a named server; pair it with
  an FPing target at the same address
  and you can separate "server slow to
  answer DNS" from "path slow".
- **EchoPing*** — a family (Http,
  Https, DNS, Smtp, LDAP, and more) that
  drives the separate `echoping`
  program.
- **SSH / AnotherSSH, TCPPing, LDAP,
  Radius, FTPtransfer** — connection and
  service timings for the protocols in
  their names.
- **TraceroutePing** — follows the path
  hop by hop, for when the ladder says
  "somewhere in the middle" and you want
  names.

Choosing is mostly asking what failure
you are imagining. Machine dead or path
broken: FPing. Service up but sluggish:
the service's own probe. When in doubt,
ping it *and* probe it — the pair is
worth more than either alone, because
the difference between the two graphs
*is* the diagnosis.

## A word on manners

Every probe generates traffic on a
schedule, forever, including the service
probes — Curl will fetch that URL every
step for as long as you run it. Against
your own services that is nothing.
Against anyone else's, keep to the rule
from chapter 1: your machines, your
network, or permission. Public DNS
servers are the customary exception —
they exist to be queried — and even
there, one watcher at five-minute
intervals is a rounding error, not a
load.
