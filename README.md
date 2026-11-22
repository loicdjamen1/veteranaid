# VeteranAid

> VeteranAid is a fictional company. Its customers, people and figures are invented for this project. Real organisations are named only as competitors or as the source of real-world rules (DOL, state veterans' departments). All data in this repository is fake.

**VeteranAid helps low-income veterans who are homeless or about to lose their home get the benefits they have earned, so they can keep or find a stable home.**

Many of these veterans never file a claim, or give up partway, because they have no fixed address, no phone and no copy of their discharge papers. State offices and veterans service organisations help for free, but they wait for a veteran to book an appointment. VeteranAid goes to the veterans who never will. Outreach and a chatbot open at any hour make first contact. A real caseworker then stays with each veteran through eligibility, documents, the claim and housing.

Veterans pay nothing. The work is funded by federal grants, mainly the Supportive Services for Veteran Families (SSVF) grant and a smaller Department of Labor HVRP grant.

**Success metric:** the share of newly enrolled eligible veterans with a complete benefits claim filed within 30 days of intake, tracked separately for each state.

## What it does

- **Public website with login.** Veterans start a case, upload documents, track their claim and chat.
- **Admin portal for staff.** A separate site where caseworkers check eligibility and manage cases, and programme managers produce grant reports.
- **Public partner API.** Referral partners such as housing nonprofits submit referrals and check their status.
- **AI chatbot** on Azure AI Foundry. It answers questions at any hour and hands off to a caseworker. It never decides eligibility, gives legal advice or promises a benefit.

## Who uses it

There are two customer organisations, VeteranAid Maryland and VeteranAid Virginia, each run under its own federal grant. **Their data must never mix.**

| Role | Uses | Sees |
| --- | --- | --- |
| Veteran | Public website, chatbot | Only their own records and uploads |
| Caseworker | Admin portal | Only the veterans assigned to them, in their own organisation |
| Programme manager | Admin portal | Aggregate figures and grant reports for their own organisation |
| Staff admin | Admin portal | User accounts and settings; no veteran records |
| Partner (API client) | Partner API | Only the status of referrals it sent |

## What's sensitive

| Data | Why it matters |
| --- | --- |
| Veteran profiles: name, date of birth, contact details,  file number, housing status | Identifies vulnerable people |
| Eligibility and income checks | Financial information |
| Uploaded documents: DD-214s, IDs, pay stubs, leases, eviction notices | Some of the most sensitive documents anyone holds |
| Medical letters | Health information |
| Caseworker notes and chatbot transcripts | Free text that can contain anything a veteran said |
| Passwords, session tokens, partner API keys, the Azure AI key | Give access to everything above |

Rules that follow from this:

- A veteran's records and uploads are visible only to that veteran and their own assigned caseworker (security requirement SR-1).
- Every upload is untrusted input: type-checked by content, malware-scanned and served only as a download.
- Veteran data is never sent to the AI platform in identifiable form and never used to train models.
- **Fake seed data only.** Fictional veterans and documents marked SAMPLE, in every environment except production. Real data is never copied here.
- Secrets live only in `.env` locally, which is git-ignored, and gitleaks blocks commits that contain them.

## Stack (planned)

Python (FastAPI) API, Postgres, simple HTML and JavaScript front ends, and Docker Compose.

| Service | Purpose |
| --- | --- |
| `web` | Public veteran website |
| `admin` | Staff admin portal, its own container and port |
| `api` | FastAPI: `/auth`, `/veteran`, `/admin`, `/partner/v1`, `/chat` |
| `db` | Postgres with three databases: `identity`, `org_maryland`, `org_virginia`, each organisation with its own database user |
| `clamav` | Malware scan for uploads |

Security events are written as one JSON object per line to stdout and `logs/security.jsonl`: `login_succeeded`, `login_failed` and `access_denied`. Passwords, tokens, names, document contents and chat text are never logged.

## Status

Plan phase. No application code yet. The design and decisions are in [`docs/`](docs/) and in Confluence.

## Setup

Requires Python 3 for [pre-commit](https://pre-commit.com).

```sh
pip install pre-commit
pre-commit install          # runs the gitleaks secret scan before every commit
pre-commit run --all-files  # scan everything once
cp .env.example .env        # fill in locally; .env is never committed
```

If gitleaks blocks a commit, remove the secret and rotate it. Do not bypass the hook with `--no-verify`.

## Chatbot spending limit

The chatbot runs in our own Azure subscription. Azure only offers a hard spending limit on credit-based subscriptions (such as the free account or a Visual Studio subscription), so we use one of those with the limit left on. On pay-as-you-go, budgets only send alerts: set a monthly budget with alerts, a low tokens-per-minute quota on the model deployment and a per-user daily message cap in the API.
