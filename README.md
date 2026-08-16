# diamondback-deploy

Public install assets and site for **[Diamondback](https://diamondback.run)** — a read-only
AWS ISM PROTECTED posture reporter and best-practice hardening scanner that deploys
into your own AWS account.

Everything here is deliberately public: a customer should be able to read the whole
stack before running anything.

| File | What it is |
|---|---|
| `install.sh` | One-shot installer, written for AWS CloudShell |
| `upgrade.sh` | Image-only in-place upgrade; the database is untouched |
| `diamondback-aws.yaml` | The CloudFormation stack the installer deploys |
| `version.json` | The published version feed the upgrade script and the console read |
| `index.html`, `docs.html` | The diamondback.run site (Cloudflare Pages) |

## Install

```bash
curl -sL https://diamondback.run/install.sh | bash -s -- --region ap-southeast-2 --admin-email you@agency.gov.au
```

Until `diamondback.run` resolves, this repository is the mirror the installer already falls
back to, so it works today:

```bash
curl -sL https://raw.githubusercontent.com/abhijitsghosh/diamondback-deploy/main/install.sh \
  | bash -s -- --region ap-southeast-2 --admin-email you@agency.gov.au
```

The service's role is granted `SecurityAudit` and `ViewOnlyAccess` and nothing more.
Diamondback reads configuration to assess it; it never writes to your account, and it
never reads the contents of your objects or databases.

The application source is private and commercially licensed. It is available to
security teams for review on request — security@diamondback.run.
