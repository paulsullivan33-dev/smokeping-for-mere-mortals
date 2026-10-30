# Recipes

**Outcome: four set-ups you can copy,
each answering a question people
actually bring to SmokePing.**

Each recipe assumes chapters 4 and 5 are
done. Configurations are additions to
the Targets and Alerts sections you
already have.

## 1. The provider evidence kit

**Question:** *Is the evening slowdown
us or them — and can I prove it?*

Build the ladder from chapter 8:

```text
+ Path
menu = Path out
title = The way out of the house

++ Router
menu = Router
title = Our router
host = 192.168.1.1
alerts = bigloss

++ PublicDNS
menu = Public DNS
title = A public DNS server
host = 1.1.1.1
alerts = bigloss
```

Run it for a week before you need it.
When the bad evening comes, screenshot
both detail graphs for the same window.
Router clean, DNS smoky or red-edged:
the fault sits beyond your walls, and
the graphs — timestamps, durations,
loss — are the evidence. Both smoky:
start at home; chapter 13 is your
friend. Keep the graphs. Providers
respond to pictures of their own bad
hours in a way they do not respond to
feelings.

## 2. Wired against wireless

**Question:** *Is the Wi-Fi the problem,
or the internet?*

Put two targets on the same distant
service if your setup allows — or, more
practically at home, run a second
SmokePing view of the same router from a
machine on Wi-Fi and compare with the
wired machine's graph of the same
router. Same target, same hours,
different smoke: the difference is the
wireless hop. Tall smoke on the Wi-Fi
view only is the signature of a
struggling radio link — interference,
distance, a busy channel — and it
exonerates everything upstream.

## 3. Watch a website, not just a machine

**Question:** *The site feels slow — is
it the site, or the network to it?*

This is the recipe the lab ran for real,
with the Curl probe (its kept
configuration, `smokeping-lab/config`,
is this recipe pointed at a server on
the same machine):

```text
*** Probes ***
+ Curl
binary = /usr/bin/curl

# and in Targets, a branch using it:
+ Web
probe = Curl
menu = Web
title = Websites

++ ExampleSite
menu = Example site
title = The example site, fetched in full measure
host = example.com
urlformat = http://%host%/
```

Pair it with an FPing target at the same
name. Ping clean, fetch slow: the
service itself is labouring. Both slow:
the path. One honest limit, straight
from the lab's numbers: a fetched page's
time includes the server's thinking time
and the transfer — it is a user's-eye
measurement, not a network one. That is
often precisely the one you want.

## 4. The alert that only cries wolf once

**Question:** *Tell me when it breaks —
not that it is still broken.*

```text
+bigloss
type = loss
pattern = ==0%,==0%,==0%,>20%
comment = No loss for three rounds, then real loss
edgetrigger = yes
```

The three clean rounds make it a
*change* alarm; `edgetrigger` makes it
two mails per incident — raised, then
cleared. Attach it to the targets whose
silence would worry you (the router, the
one server that matters) and to nothing
else. An alert on everything is an alert
you learn to ignore, which is worse than
none. Chapter 9 covers the pattern
language and the history an alert needs
before it can speak at all.
