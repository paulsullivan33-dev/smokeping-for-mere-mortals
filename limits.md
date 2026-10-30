# Limits

**Outcome: you can name what SmokePing
cannot tell you, before it fails to tell
you at an awkward moment.**

Check: for each limit below, say whether
it would matter for the question you
brought to chapter 1. Two or three
"no"s and the tool fits your purpose.
A "yes" sends you to chapter 11's
neighbours to fill the gap — on purpose,
not by accident.

## Latency and loss, nothing else

SmokePing times round trips and counts
missing replies. It cannot measure
bandwidth — a path can ping beautifully
at 5 ms and still move files slowly —
and it cannot see *content*: it knows a
page took 200 ms, not whether the page
was the right one. Speed tests and
uptime checkers answer those questions;
this tool declines to.

## It cannot say why

A graph proves *that* a path turned
bad, *when*, and — with a ladder —
roughly *where along the path*. It never
says why. Congestion, a failing cable, a
neighbour's microwave: those need other
evidence. Anyone who has waved a
SmokePing graph as proof of a *cause*
has over-claimed; wave it as proof of a
*symptom with timestamps*, which is
already formidable.

## The view from one chair

Measurements are taken from where the
daemon sits. Your house's path to a
server may be dreadful while the rest of
the world's is fine, and SmokePing will
report your dreadful path faithfully —
which is the truth you live with, but
not the whole truth. Master/slave mode
exists to add viewpoints (slaves report
to a master), and this guide describes
it in outline only; the lab ran one
chair.

## The network may refuse to be pinged

ICMP is the first traffic many networks
throttle, filter, or answer lazily. A
target can be healthy and still answer
pings slowly or not at all, because it
has better things to do — some routers
deprioritise replying to pings while
forwarding traffic at full speed. And
some environments block the probe
outright: this guide's own lab could not
open a ping socket at all, even to
itself (chapter 6). A SmokePing graph is
evidence about *ping behaviour*; usually
that tracks the truth about the path,
and the exceptions are learnable, target
by target.

## The step is a sieve

At the default five-minute step, a
ninety-second outage can vanish between
rounds or appear as one spoiled round.
Shorter steps measure finer but cost
more traffic and attention, and the step
is fixed when the data files are
created. SmokePing is a historian of
trends and bad patches, not a recorder
of blips.

## Old detail fades by design

RRD files summarise as data ages: the
year view is built from coarse averages,
so last spring's worst single round
survives as a bump, not a spike with
numbers. This is the price of a file
that never grows — and a fair price —
but cite recent graphs in arguments,
and export the numbers (`rrdtool fetch`,
chapter 6) for any incident you may
need to prove months later.

## Alerts are only patterns and post

An alert fires when history matches your
pattern — which means after the fact,
in units of rounds, and only if the
pattern was written for the shape the
trouble actually took. Mail depends on a
working sendmail and a read inbox. And
nothing in SmokePing will *fix* anything:
it is a witness, not a mechanic. The
witness is excellent. Bring your own
mechanic.
