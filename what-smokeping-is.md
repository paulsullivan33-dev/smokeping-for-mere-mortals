# What SmokePing is

**Outcome: you can say what SmokePing
remembers, why that beats testing by
hand, and whose machines you may point
it at.**

Check: by the end of this chapter you
should be able to finish this sentence —
"SmokePing will tell me exactly how bad
the connection to my router was at nine
o'clock last Tuesday, because …". If the
ending is "because it was awake then,
testing, and it kept the results," you
have the idea.

## A snapshot and a film

The `ping` command is a snapshot. It
sends a few test packets — small "are
you there?" messages — times how long
each answer takes, prints the times, and
forgets them. Run it while the network
feels slow and you learn something about
that second. Run it afterwards and you
learn that the network is fine *now*,
which is not the question.

SmokePing is the same test run as a
film. A small background program — a
**daemon** — repeats the test every few
minutes, forever: twenty small pings to
each machine you named, timed one by
one. Every round is written into a
database built for keeping measurements
over time. Weeks later you can open a
web page and see the whole history as a
graph: the bad patch at nine last
Tuesday is still there, with a start, a
duration, and a shape.

That shape is the point. Two connections
can average the same speed and feel
completely different. One answers every
ping in about the same time. The other
answers most pings quickly and some
very slowly — fine for web pages, awful
for calls and games. A single average
cannot tell them apart. SmokePing's
graphs can, because each round is drawn
as a line for its middle value **plus**
a grey smudge — the smoke — for how
spread out that round's answers were.
Chapter 7 teaches you to read it.

## What people use it for

- **Winning the argument with evidence.**
  "The connection dies every evening"
  becomes a graph you can send: it starts
  at 20:55, it lasts about an hour, and
  it happens at the provider's side of
  your router, not yours.
- **Watching a change.** New router, new
  provider, new Wi-Fi box? The before and
  after are both on the graph, measured
  the same way.
- **Catching the fault you never see.**
  The drop that happens at 3 a.m., the
  slowdown that only strikes on Sundays —
  the daemon was awake for all of them.
- **Being told, instead of noticing.**
  Alerts can mail you when a pattern you
  described — say, loss appearing after
  three clean rounds — shows up in the
  data. Chapter 9 covers them.

## The one rule

SmokePing is an *active* measurement
tool. It does not watch traffic go by;
it generates traffic — pings, DNS
lookups, page fetches — aimed at the
targets you name. That is perfectly
reasonable against your own machines,
your own network, and public services
that exist to be tested. It is not
reasonable against machines belonging to
other people who never agreed to it.
Keep your target list to networks you
own or have permission to test. This
guide's examples stick to your own
machines, your router, and well-known
public DNS servers, and yours should too.

## What it is not

SmokePing measures **latency** — how
long a round trip takes — and **loss** —
how many test packets never come back.
It does not measure bandwidth (how much
data fits per second), it does not
inspect your traffic, and it cannot say
*why* a path turned slow. Chapter 12
lists the limits in full. Within its one
job, though, it is close to unbeatable:
cheap to run, patient, and very hard to
argue with.
