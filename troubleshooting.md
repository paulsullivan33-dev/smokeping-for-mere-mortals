# Troubleshooting

**Outcome: for each common failure, you
have the one check that names it and the
fix that follows.**

Check: work down the list in order —
every entry assumes the ones above it
passed. Start with the configuration
check even if "nothing changed": the
config is the one suspect that can be
cleared in five seconds.

## "syntax OK" — or not

```sh
sudo smokeping --check --config=/etc/smokeping/config
```

**Symptom:** the daemon will not start,
or starts and ignores your edit.
**Cause, usually:** a configuration
error, or an edit to the wrong file
(Ubuntu's split `config.d` layout edits
easily into the wrong section file).
The checker names the line. Fix the
line, re-check, then restart the daemon
— it reads the file at startup, not
continuously.

## Every target shows total loss, from the first round

**Check:** read the data, not the graph
(chapter 6). If loss is at maximum *and*
every ping column is `U`, run the probe
by hand:

```sh
fping -C 5 127.0.0.1
```

**Cause, if fping complains** — "can't
create socket", permission errors: the
probe cannot run. The package normally
grants `fping` the permission it needs;
a hand-installed binary, a container, or
a sandbox may not. This guide's lab hit
exactly this (transcripts t03 and t05):
the graphs faithfully recorded a probe
that never sent a packet. Fix the
permission or the environment — no
configuration change can make a blocked
probe measure.

## The graph is empty or frozen

**Checks, in order:** is the daemon
running (`systemctl status smokeping`)?
Is the newest RRD row fresh
(`rrdtool lastupdate`)? Did you wait a
full step after starting?

**Causes:** a stopped daemon leaves the
web page cheerfully showing old graphs —
the page is drawn on request from files,
and old files draw fine. A frozen
`lastupdate` timestamp with a running
daemon points at the probe (previous
entry) or at unwritable `datadir`.
Brand-new targets draw empty for their
first step by design; the data grows in
from the right edge (chapter 5).

## You changed the step and everything broke

**Symptom:** daemon errors about RRD
files, or targets refuse to update.
**Cause:** RRD files are created with
the step baked in. Changing `step` (or
`pings`) in the Database section does
not reshape existing files. Either
delete the affected `.rrd` files and
let SmokePing recreate them — accepting
the history loss — or restore the old
Database values. This is why chapter 4
asks you to decide the step *first*.

## The web page will not load

**Checks:** does the CGI run at all?
The lab executed the package's
`smokeping.cgi` directly with the config
as its argument — the same trick works
on your machine to separate "CGI broken"
from "web server broken":

```sh
sudo /usr/lib/cgi-bin/smokeping.cgi /etc/smokeping/config </dev/null | head
```

**Causes:** the CGI needs to write its
image cache (`imgcache` in General) and
read the RRDs; permission failures
there are the usual story, followed by
a web server missing the CGI module or
the shipped `smokeping.conf` snippet
not enabled (`a2enconf smokeping`,
`a2enmod cgid`, restart Apache).

## Alerts never fire — or fire constantly

**Checks:** does the pattern's length
fit the incident? A four-round pattern
cannot match a two-round blip, and says
nothing at all until four rounds exist
(chapter 9). Is `sendmail` real and
the `to` address read?

**Causes:** a pattern written for
perfection (`==0%` rounds required)
against a target that is never perfect;
mail landing in an unwatched mailbox;
or — the lab's deliberate set-up —
`sendmail` pointed at `/bin/true`,
which succeeds at sending nothing.
The daemon log records alert evaluations
in debug mode; one `--debug` round
shows each alert's verdict, as t05
shows for `bigloss`.

## One target is fine, its twin is not

Two targets at the same address measured
by different probes (FPing vs Curl, say)
disagreeing is not a fault — it is the
design working. Ping answers prove the
network stack replied; a fetch proves
the service worked. Read the difference
as the diagnosis (chapter 8), not as an
error to reconcile.
