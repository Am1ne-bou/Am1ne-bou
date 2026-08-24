# Mohamed Amine Boussenna

Final-year Telecom engineering student, ENSEIRB-MATMECA - Bordeaux INP. Looking
for a 6-month end-of-studies internship from February 2027. Available in Paris.

Currently interning at Devoteam Morocco (Cyber Trust practice), building the
automation layer for an internal platform: Terraform for host, firewall and DNS,
Ansible roles that re-run cleanly, one command for full deploy and teardown,
supporting tooling in Go.

Hardening runs as part of the deploy rather than as a later step - ephemeral
hosts rebuilt from code, key-only SSH behind a default-deny firewall, services
bound to loopback and reachable only through the proxy. Secrets stay out of the
repository, enforced by a CI job that fails the build, and a verification stage
checks the deployed result from outside the host rather than trusting exit codes.

## What I run

### [ssh-honeypot](https://github.com/Am1ne-bou/ssh-honeypot) - Go

Medium-interaction SSH honeypot. Live on a public VPS since May 2026, running
unattended under systemd with log rotation and CI.

190,862 authentication attempts from 3,069 unique source IPs, through 2026-08-23
- see [FINDINGS.md](https://github.com/Am1ne-bou/ssh-honeypot/blob/main/FINDINGS.md).

Emulated shell (~80 commands, virtual filesystem) and a full SCP wire-protocol
implementation - enough that bots run their whole kill chain and drop real payloads
on it. Containment is treated as seriously as the bait: unprivileged systemd service
(NoNewPrivileges, ProtectSystem=strict), never root, the real sshd moved off port 22,
captured payloads quarantined read-only and sha256-named.

Sessions are logged as JSON and grouped offline by exact command sequence - hash the
commands, count what shares a hash. Echo-injection runs have to be folded first, or the
bot that writes a binary in 43,000 echo commands makes every session it touches unique.
Volume points the wrong way: the largest cluster is 139,336 sessions writing "ok" to
stdout, while the family worth reading is six sessions looking for Telegram session
files and GSM modem device nodes. 
Passive payload triage only: no reverse engineering, no alerting in service.

### [DOR](https://github.com/Am1ne-bou/DOR) - Go

Anonymous overlay network. The threat model is the point: the adversary is not a
reader of the payload but a relay that is itself hostile, or an observer watching
several relays at once - and encryption alone does not answer that.

Hybrid onion encryption (RSA-2048-OAEP per hop, AES-256-GCM payload, so each
layer is authenticated); every relay strips one layer and learns only the next.
The harder parts hold against an observer rather than a reader: NACKs rewritten
at every hop, fixed 4 KB fragments so size stops being a signal, TTL-scoped
replay rejection.

7-person school project (ENSEIRB-MATMECA, S8), this fork maintained solo. A
learning implementation, not audited and not production crypto: the point was
designing against traffic analysis, not competing with Tor. Coding is paused -
the open question I could not answer, how a node chooses peers when any of them
may be hostile, turns out to be an active research problem, so that is what I
read now instead of adding code.

### [IRC_Chat](https://github.com/Am1ne-bou/IRC_Chat) - C

Group coursework - a multi-client IRC server over TCP/IP, all clients on one
thread through poll(). The security layer I added alone, afterwards, and
"afterwards" is the point: the application shipped everything in clear text and
stored passwords unprotected.

TLS through OpenSSL with an X.509 certificate, layered under the existing binary
protocol rather than replacing it, so the wire format and client code survived
unchanged. Credentials moved to bcrypt with a per-user salt - bcrypt and not
SHA-256 on purpose, because a password hash has to be deliberately slow and
tunable where SHA-256 is fast by design.

What it still does not do: no rate limiting on authentication attempts, and
direct client-to-client file transfers run over a plain TCP socket, outside the
TLS tunnel.

Bolting security on afterwards taught me what it costs - which is why in my
current internship the hardening runs inside the deploy code instead of as a
later step.

---

[LinkedIn](https://www.linkedin.com/in/mohamed-amine-boussenna-25806738a/)

<!-- TODO: once the GitHub Pages site is live, restore the site link here:
     [LinkedIn](https://www.linkedin.com/in/mohamed-amine-boussenna-25806738a/) - [Am1ne-bou.github.io](https://Am1ne-bou.github.io) -->

