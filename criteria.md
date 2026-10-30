# Writing criteria for this guide

Every "for Mere Mortals" guide is written to the same nine-point
standard, so a reader who trusts one can trust the rest. Here it
is, with the verification notes for this particular guide below.

1. **Fix it first.** Reader outcomes lead, not action items: each
   chapter opens by naming the outcome it delivers, shows a check
   the reader can run to confirm they got it, and a reasonable
   reader finishes able to state what was done, what it means,
   and what to try next.
2. **Plain language.** Short sentences, everyday vocabulary,
   technical terms defined on first use. No slang, and no
   idioms that only make sense in one country — the reader may
   be translating as they go.
3. **No dead ends.** A guide never tells the reader to do
   something it cannot show them how to verify. Every claim a
   reader relies on — an install, a command, an output — is
   something this guide demonstrates or clearly labels
   documented-only below.
4. **Live-verified where it matters.** Procedures are run for
   real in the guide's lab wherever the build machine allows it,
   and the transcripts kept in the repository are the evidence.
   What could not be run is listed honestly below, never
   presented as if it had been.
5. **General audiences.** Nothing in this guide — examples,
   fixtures, names, addresses — is modelled on any particular
   reader or author's real life, network, or organisations.
   The lab's targets are this machine itself and a web page
   served on it; the guide's examples are a generic home router
   address and a well-known public DNS server.
6. **Standalone.** A reader needs this guide, its lab, and
   nothing else. Where another guide would genuinely help, it
   is pointed to once, by name, and the chapter still completes
   without it.
7. **Honest about limits.** Chapter 12 names what the tool
   cannot do: latency and loss only, no causes, one viewpoint,
   networks that refuse to be pinged, a step that sieves out
   short faults, history that fades as it ages, and alerts that
   witness but never fix.
8. **The ethics rule is in the text, early.** SmokePing is an
   active measurement tool — it generates test traffic at the
   targets it is given — so chapter 1 and the README state the
   rule before any procedure depends on it: measure machines
   and networks you own, or that you have permission to test,
   and never strangers'.
9. **Builds clean.** The site builds with MkDocs in strict mode
   with no warnings, and a fresh reader following the chapters
   in order reaches the stated outcome of each.

## Verification notes for this guide

The build machine is a shared Ubuntu 24.04 sandbox with no
package-manager session and unusual network rules, both of
which shaped the lab and are part of the record. SmokePing
**2.8.2** (reporting itself as 2.008002), RRDtool 1.7.2 and
fping 5.1 were taken from the ordinary Ubuntu 24.04 packages —
the same `.deb` files `apt install smokeping` installs —
downloaded with a lab-private apt state and unpacked into a
folder, then run from there with library paths pointed at the
unpacked tree. Nothing was installed system-wide; the versions
and the run-everything-from-the-tree method were verified
before any configuration was written (transcripts t00–t02).
All transcripts live in `smokeping-lab/transcripts/`; the two
configurations the lab ran are `smokeping-lab/config` (Curl
probe) and `smokeping-lab/config.fping` (FPing probe), and the
kept graph is `smokeping-lab/graph-localweb.png`.

The sandbox's network is the lab's defining fact, and it was
measured, not assumed (transcript t03): ICMP raw sockets and
ICMP datagram sockets are refused even to root (the effective
capability set lacks CAP_NET_RAW), UDP sends are refused even
on loopback, and TCP on loopback works. There was therefore no
configuration of SmokePing — FPing included — that could
collect a genuine ping time in this lab, and the guide says so
wherever it matters instead of quietly substituting numbers.

Run for real, with transcripts as evidence:

- Downloads and unpacking of the 28 packages of
  `apt install smokeping`'s dependency set, and version checks
  of all three programs from the unpacked tree (t01, t02).
- `fping` and the system `ping` failing against 127.0.0.1, and
  the socket-level tests that pin down why (t03).
- Two complete configurations written and both validated by
  `smokeping --check` — including two alert patterns, `bigloss`
  (loss) and `slowweb` (rtt), which the checker compiles (t04).
- One full FPing round in `--debug` mode against 127.0.0.1:
  the daemon created the RRD with its full column layout,
  executed the real `fping` command line, recorded its exit
  failure as loss 5 with all times unknown, compiled and
  evaluated the `bigloss` detector, and reported "no match"
  for want of history (t05).
- The daemon running in its normal mode for about six minutes
  with the Curl probe against a web server on the same machine
  (Python's `http.server` serving a one-page site on loopback):
  eleven rounds at the lab's shortened 30-second step, then a
  clean shutdown on SIGTERM, all in its own log (t06).
- The stored measurements read back independently:
  `rrdtool lastupdate` showing loss 0 with five real fetch
  times, and `rrdtool fetch` showing eleven populated rows —
  step-averaged medians from 8.3 ms in the first round settling
  to about 1.2–1.4 ms — with honestly empty (-nan) rows before
  the start and after the stop (t07).
- SmokePing's own `smokeping.cgi`, run as a plain CGI with the
  lab configuration, serving the target page and generating
  its graphs through SmokePing's graph code: the "Last 3
  Hours" detail graph is a 697×310 PNG of 24,081 bytes whose
  statistics line (median 3.5 ms avg, 8.3 ms max, 1.2 ms min,
  0.00% loss) matches the fetched data (t08). The PNG was also
  viewed directly: the measured series is visible at the
  window's right edge, as chapter 5 describes.

Documented only — described from the package's own files and
the project's documentation, not run, and the text does not
claim otherwise:

- The `apt install` route itself (the lab's sandbox has no
  package-manager session; the lab used the identical packages
  unpacked, and chapter 3 says so).
- Any real ping measurement, any internet target, and every
  probe besides FPing (configured, checked, and given a real
  round that the sandbox blocked) and Curl (which supplied all
  measured data). The remaining probes are characterised from
  the module list shipped in the package.
- Serving the web page through Apache or nginx — the Apache
  snippet quoted in chapter 5 was read from the shipped
  package file `/etc/apache2/conf-available/smokeping.conf`;
  the CGI itself was run directly.
- An alert actually firing and mailing: patterns were
  configured, compiled by the checker, and evaluated in debug
  output, but conditions never matched during the lab's short
  run, and `sendmail` was deliberately pointed at `/bin/true`.
- Master/slave setups, FPing6 and the continuous and remote
  FPing variants in operation, and the neighbour tools of
  chapter 11, which are characterised, not run or benchmarked.

Fixtures are this guide's own and deliberately generic: the
lab measured only the machine it ran on — itself, by loopback,
and a static page it served itself. No person's network,
addresses, or traffic appear anywhere; the one real name in
the evidence is the sandbox host's own hostname, which
SmokePing prints into its graph titles about itself.
