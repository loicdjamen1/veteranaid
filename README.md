# VeteranAid

> VeteranAid is a fictional company. Its customers, people and figures are invented for this project. Real organisations are named only as competitors or as the source of real-world rules (VA, DOL, state veterans' departments).

**VeteranAid helps low-income veterans who are homeless or about to lose their home get the VA benefits they have earned, so they can keep or find a stable home.**

Many of these veterans never file a claim, or give up partway, because they have no fixed address, no phone and no copy of their discharge papers. State offices and veterans service organisations help for free, but they wait for a veteran to book an appointment. VeteranAid goes to the veterans who never will. Outreach and a chatbot open at any hour make first contact. A real caseworker then stays with each veteran through eligibility, documents, the claim and housing.

Veterans pay nothing. The work is funded by federal grants, mainly the VA's Supportive Services for Veteran Families (SSVF) grant and a smaller Department of Labor HVRP grant. We run a Maryland programme and a Virginia programme, each under its own grant, budget and grant officer.

The platform is how a small nonprofit scales without hiring at the same rate:

- **Public website** with login, where veterans start and track their case
- **Admin portal** where caseworkers and state programme managers check eligibility, manage cases and produce grant reports
- **Public partner API** that lets referral partners such as housing nonprofits send veterans in
- **AI chatbot** (Azure AI Foundry or AWS Bedrock, to be confirmed) that answers questions at any hour and hands off to a caseworker

**Success metric:** the share of newly enrolled eligible veterans with a complete VA benefits claim filed within 30 days of intake, tracked separately for each state.

## The rule above all others

Maryland and Virginia data never mix. A caseworker or programme manager in one state must never see another state's veterans, through the portal, the API or the chatbot. Veteran data is never used to train AI models and never leaves our approved cloud. When speed and separation conflict, separation wins.

## Status

Plan phase. This repository holds no application code yet, only the project setup.

## Setup

Requires Python 3 for [pre-commit](https://pre-commit.com).

```sh
pip install pre-commit
pre-commit install          # runs the gitleaks secret scan before every commit
pre-commit run --all-files  # scan everything once
cp .env.example .env        # fill in locally; .env is never committed
```

If gitleaks blocks a commit, remove the secret and rotate it. Do not bypass the hook with `--no-verify`.

## Never commit

- Secrets, keys or connection strings (use `.env` locally and the cloud secret store in deployed environments)
- Veteran data of any kind, real or realistic: profiles, DD-214s, medical letters, leases, notes or chat transcripts. Test data must be clearly synthetic.

## Where the decisions live

The company story, architecture and trust boundaries, data inventory and decisions log are kept in Confluence.
