# DNS records for diamondback.run

These are **DNS records, not web content**. Nothing here is served as part of the site —
the folder travels with the bundle so the records are to hand when you edit the zone.

Add them in Cloudflare → your domain → DNS → Records, or import `diamondback.run.zone`
via DNS → Records → Import and Export.

## What is already in place

    MX      24 route1.mx.cloudflare.net      Cloudflare Email Routing
    MX      33 route2.mx.cloudflare.net
    TXT @   v=spf1 include:_spf.mx.cloudflare.net ~all

Cloudflare adds those when Email Routing is enabled. Leave the SPF as Cloudflare wrote it:
Email Routing forwards through those hosts, and tightening it by hand can break forwarding.

## What is missing

DMARC. Without it, anyone can send mail claiming to be from diamondback.run and no receiving
server has been told what to do about it. That matters more here than for most domains:
Diamondback checks its customers for SPF, DKIM and DMARC, and a compliance vendor whose own
domain fails the check it runs on you is the first thing a sharp prospect will test.

Diamondback sends no mail — no mail library, no SES client, no SMTP, and the Cognito invite at
install is sent by AWS from inside the customer's own account. A domain that sends nothing can
safely publish the strictest policy, because there is no legitimate mail to break.

## Step 1 — observe (recommended first)

    Type   TXT
    Name   _dmarc
    Value  v=DMARC1; p=none; rua=mailto:dmarc@diamondback.run; fo=1; adkim=s; aspf=s

`p=none` asks receivers to report but not to act. Run it for a week or two and read the
aggregate reports: they will show whether anything legitimate is sending as your domain that
you had forgotten about. Route `dmarc@` to yourself first, or enable the Cloudflare catch-all,
or the reports go nowhere.

## Step 2 — enforce

Once the reports show nothing legitimate is sending, switch the policy:

    Type   TXT
    Name   _dmarc
    Value  v=DMARC1; p=reject; rua=mailto:dmarc@diamondback.run; fo=1; adkim=s; aspf=s

`p=reject` tells receivers to discard mail that fails. `adkim=s` and `aspf=s` require strict
alignment rather than allowing subdomains to pass on a parent's behalf.

If you would rather not read reports at all, going straight to step 2 is defensible for a
domain that sends nothing — you simply lose the chance to discover that something does.

## Optional: state that nothing sends from subdomains

    Type   TXT
    Name   *
    Value  v=spf1 -all

A wildcard SPF hard-fail on subdomains stops anyone spoofing, say, mail.diamondback.run.
Add it only if you are not planning to send from a subdomain later.
