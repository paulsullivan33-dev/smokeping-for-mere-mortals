# Alerts

**Outcome: you have an alert pattern
that describes a bad patch in plain
terms, SmokePing has compiled it without
complaint, and you know what happens
when it matches.**

Check: `smokeping --check` passes with
your Alerts section in place (chapter
4). The checker compiles patterns; in
the lab's debug run you can watch one
being turned into its detector
(transcript t05 prints the compiled form
of the lab's `bigloss`). If the pattern
were malformed, the check would fail
before the daemon ever started.

## Patterns describe a shape in history

An alert is not "loss above 20%". It is
a sentence about the *last few rounds*,
read oldest to newest. The lab's running
configuration carries two:

```text
+bigloss
type = loss
pattern = ==0%,==0%,==0%,>20%
comment = No loss for three rounds, then more than a fifth lost
edgetrigger = yes

+slowweb
type = rtt
pattern = <0.010,<0.010,>0.100
comment = Two fast rounds, then one round slower than a tenth of a second
edgetrigger = yes
```

Read `bigloss` aloud: *loss exactly zero
for three rounds, then loss above twenty
percent.* The three quiet rounds matter
as much as the loud one. They are what
makes this a **change** detector — it
fires when a healthy target *turns*,
not on a target that has always been
lossy (which would mail you forever) and
not on a single blip with no clean
history before it. `slowweb` does the
same for time instead of loss: two
rounds under a hundredth of a second,
then one over a tenth. The `type` says
which column of each round the pattern
reads — `loss` or `rtt` (round-trip
time).

The pattern language is small:
comparisons (`>`, `<`, `==`, `>=`),
percentages for loss, seconds for rtt,
`*N*` to repeat a condition N rounds,
and `U` to match an unknown round.
Compound patterns chain smaller named
ones. It rewards the same discipline as
the target tree: write the sentence you
would say out loud — "fine, fine, fine,
then bad" — and translate it literally.

## Edge triggering

With `edgetrigger = yes`, an alert mails
**twice per incident**: once when the
pattern starts matching ("was raised")
and once when it stops ("was cleared").
Without it, a matching alert mails on
every round while the pattern holds —
for a bad weekend, that is hundreds of
messages saying one thing. Edge
triggering is almost always what you
want; set it and forgive yourself the
inbox.

## Alerts need history before they can speak

The lab's FPing round ended with a line
worth understanding (transcript t05):

```text
Alert "bigloss": no match for target .../Loopback
```

`bigloss` demands four rounds of history
— three clean, one bad. One round in,
there is nothing to match against, so
"no match" is the only possible verdict
even with that round at total loss. New
alerts are always silent for their
pattern's length after a restart. Plan
for it: an alert is a promise about the
*next* bad patch, never about catching
the one in progress while you set it up.

## Where the mail goes

Matches are mailed to the Alerts
section's `to` address, from its `from`
address, through the `sendmail` named
in General. The lab pointed `sendmail`
at `/bin/true`, the program that does
nothing successfully — its alerts are
configured and checked, and deliberately
send nothing (a sandbox has no mailbox
worth mailing). On your machine, point
these at addresses you actually read,
and test with a pattern you can trigger
by hand — unplugging a watched machine
is the traditional method — before you
rely on one. A monitoring system whose
alerts land in a dead mailbox is a
graphing system with extra steps.
