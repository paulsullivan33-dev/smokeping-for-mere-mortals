# The first graph

**Outcome: the daemon is running, one
round has been measured, and you have
seen the web page SmokePing draws —
including your first graph.**

Check: open the SmokePing page (on the
install machine, `http://localhost/smokeping.cgi`,
or your server's address). Your target's
name appears in the menu; clicking it
shows a page titled "SmokePing Latency
Page for …" with graphs under it. The
lab's page announced exactly that title
for its one target (transcript t08).

## Start, then wait one round

```sh
sudo systemctl start smokeping
systemctl status smokeping
```

The daemon's own log of a startup is
short. The lab's said (transcript t06):

```text
Smokeping version 2.008002 successfully launched.
Not entering multiprocess mode for just a single probe.
Curl: probing 1 targets with step 30 s and offset 19 s.
```

Yours will name FPing instead of Curl,
and (at the default step) announce a
five-minute rhythm. Note the **offset**:
each probe starts its round a little
into the step, so rounds do not all land
on the same second. Your first graph
needs one full round plus the moment
RRDtool writes it — give it a step and a
half before you judge anything. An
empty page in the first minutes is a
young install, not a broken one.

## What the page shows

The front page is the target tree from
your configuration, with a small
**overview** graph per target — the last
ten hours squeezed into a strip. Click a
target and you get its **detail** page:
the same data at the ranges you listed
in Presentation — last 3 hours, 30
hours, 10 days, 360 days in this guide's
configuration. Young installs draw the
early ranges mostly empty, with the data
growing in from the right edge. That is
exactly what the lab's kept graph shows:
six minutes of measurements at the right
of a three-hour window
(`smokeping-lab/graph-localweb.png`).

Click a detail graph and you reach the
navigator, where you can choose any
start and end and redraw. Times are the
viewpoint of the machine running
SmokePing — a graph of "last night" is
that machine's last night.

## How the page gets served

There is no SmokePing web server; the
page is drawn by `smokeping.cgi`, a CGI
program, and something must run it. The
Ubuntu package ships the Apache wiring
as a file this guide's lab read from the
package itself, `/etc/apache2/conf-available/smokeping.conf`:

```apache
ScriptAlias /smokeping/smokeping.cgi /usr/lib/cgi-bin/smokeping.cgi
Alias /smokeping /usr/share/smokeping/www
```

With Apache installed, enabling that
snippet and the CGI module
(`sudo a2enconf smokeping; sudo a2enmod cgid`)
puts the page at `/smokeping/` on the
server. People also run the CGI under
nginx with a FastCGI wrapper; the shape
is the same.

Honest boundary: the lab did not run
Apache. It ran the package's own
`smokeping.cgi` directly, as a plain
CGI, with the lab configuration as its
argument — the same program, the same
code path that draws the pages and
graphs, exercised without a web server
(transcript t08). The graphs in this
guide came from that run. The Apache
route above is from the package's own
shipped configuration, labelled here as
described-not-run, per this series'
rules.
