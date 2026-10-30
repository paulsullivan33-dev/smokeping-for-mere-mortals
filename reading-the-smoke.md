# Reading the smoke

**Outcome: given any SmokePing graph,
you can say three things about the
period it shows — how fast, how steady,
and how lossy — and point at where on
the graph each answer lives.**

Check: open the lab's kept graph
(`smokeping-lab/graph-localweb.png`) and
find, before reading on: a median line,
a smudge around it, and a statistics
line at the bottom beginning "median
rtt:". All three are there.

## The parts of a graph

**The median line.** Each round
contributes one point: the median of
that round's pings — the middle value,
so one freak reply cannot drag it. The
line is the connection's typical speed
over time. On the lab graph the line
sits near the bottom: this machine
answering itself is fast, and the scale
(a full ten milliseconds) is chosen by
the tallest spike, not the typical value.

**The smoke.** Around the line, in grey,
is the round's spread. Every individual
ping time in the round is plotted, and
the eye reads the cloud as smoke. Thin
smoke hugging the line means every ping
in the round took about the same time —
a steady connection. A tall smudge means
the round's replies were scattered —
some fast, some slow. That scatter is
**jitter**, and jitter is what ruins
voice calls and games while leaving
downloads — and averages — looking fine.
A connection can get *more smoked*
without its median moving at all; this
is the graph's whole reason to exist.

**The loss colours.** Along the bottom
of each round's column, a colour strip
encodes how many pings were lost, with
the key printed under the graph: green
for none, through blue and purple to
red for all of them — the lab graph's
key reads "loss color: 0, 1, 2, 3, 4,
5/5", because its rounds held five
fetches. A graph can be low, tight, and
still alarming, if its bottom edge burns
red: fast *when it answers at all* is a
failure mode of its own.

**The statistics line.** Under each
graph, SmokePing prints the period's
summary. The lab graph's is worth
reading whole:

```text
median rtt:  3.5 ms avg   8.3 ms max   1.2 ms min   1.4 ms now
packet loss: 0.00 % avg   0.00 % max   0.00 % min   0.00 % now
```

— plus the spread (sd) and the probe
note: "5 URLs using curl(1) every 30s".
On a ping graph that last line reads
"20 pings … every 5m" or similar; it is
the graph telling you what a round was.

## Three shapes worth recognising

- **The cliff.** The median steps up to
  a new level and stays. Something
  changed state: a route moved, a queue
  filled, a neighbour started a nightly
  backup. Cliffs have causes with start
  times; the graph gives you the time.
- **The bonfire.** The median barely
  moves, but the smoke billows for an
  hour every evening. Congestion that
  hurts interactive use while averages
  stay innocent — the classic "fine on
  the speed test, terrible on calls."
- **The red edge.** Loss colours flare
  while times stay low. Something on the
  path is dropping packets under load,
  or a wireless link is struggling. If
  your router's graph stays green while
  an internet target burns, you have
  located the argument's winner
  (chapter 10, first recipe).

Read graphs in that order — colour
first (did things *arrive*?), median
second (how fast?), smoke last (how
steadily?) — and a glance becomes a
diagnosis.
