# Proving the numbers

**Outcome: you can open a target's data
file and read its measurements with your
own eyes — and you can tell the
difference between "the target was down"
and "the probe never ran."**

Check: point `rrdtool lastupdate` at a
target's RRD file. You should see a row
of recent numbers. In the lab
(transcript t07):

```text
$ rrdtool lastupdate LocalWeb.rrd
 uptime loss median ping1 ping2 ping3 ping4 ping5

1793349439: U 0 0.002102 0.001097 0.001405 0.002102 0.002941 0.004508
```

Loss 0, a median of about two
milliseconds, five individual times, all
present. That is a measurement. The rest
of this chapter is learning to read the
other possible answers.

## The columns

Every target's RRD file has the same
columns, set when the file is created:

- **uptime** — for probes that report
  how long the target has been up; most
  leave it `U`.
- **loss** — how many of the round's
  pings got no answer, out of the
  `pings` you configured.
- **median** — the middle ping time of
  the round, in seconds.
- **ping1 … pingN** — every individual
  time, kept so the graph can draw the
  smoke around the median.

`rrdtool fetch` reads history instead of
the latest row. Over the lab's run it
returned eleven populated rows: loss 0
in every round, step-averaged medians
between 8.3 ms for the very first round
and about 1.2–1.4 ms once settled —
early rounds on a busy machine are often
the slow ones, which is itself a thing
graphs will teach you. Rows before the
daemon started and after it stopped read
`-nan`. **NaN means "no measurement was
made"** — it is not zero, and SmokePing
draws it as a gap, not as perfection.

## The case of the probe that never ran

Now the other answer. Here is the entire
stored result of the lab's FPing round
against this machine itself (transcript
t05):

```text
1793349093: U 5 U U U U U U
```

Loss 5 out of 5, every time unknown.
Read naively: *this machine was
completely unreachable — from itself.*
The truth was in the daemon's debug
output, one line earlier: SmokePing ran
`fping -C 5 -q -B1 -r1 -i10 127.0.0.1`,
and fping "returned with exit code
1024". Run by hand, fping says why
(transcript t03):

```text
fping: can't create socket (must run as root?)
```

The lab sandbox refuses the socket type
pinging needs to everyone, root included
— so no ping was ever sent, and SmokePing
faithfully recorded the shape of that
failure: total loss, no times. The
database was not lying. It was reporting
a measurement that never happened, in
the only columns it has.

This is the most important reading skill
in the book:

- **Loss at maximum *and* every ping
  time unknown, from the very first
  round** — suspect the probe, not the
  target. Run the probe's program by
  hand (`fping -C 5 127.0.0.1`) and read
  what it says.
- **Loss at maximum but earlier rounds
  were fine** — the target, or the path
  to it, really did break at that hour.
- **Some loss, times present** — a
  genuinely struggling path. Real data.

## Trust, but verify once

You do not need to read RRD files
weekly. Do it once, when the install is
new, exactly as this chapter did:
one target, `lastupdate`, one `fetch`.
After that, when a graph surprises you,
you will know the way back down to the
numbers it was drawn from — and you will
check the probe before you blame the
network.
