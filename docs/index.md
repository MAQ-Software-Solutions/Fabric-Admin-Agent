# Fabric Admin Agent — Documentation

Welcome. Everything you need to evaluate, install, and run the Fabric Admin Agent is listed here.

**New to the agent?** Start with [What it does](overview/what-it-does.md).
**Ready to install?** Go to [Prerequisites](setup/prerequisites.md).
**Already running it?** Jump to [Operations](#operations).

---

## Overview

What the agent does and who it's for — no technical background needed.

| Page | What you'll find |
|---|---|
| [What it does](overview/what-it-does.md) | The problem, how the agent solves it, what it watches for, and where your data lives |
| [Key features](overview/key-features.md) | Every capability, grouped by what you're trying to achieve |
| [Use cases](overview/use-cases.md) | Real situations by role — tenant admins, capacity owners, finance, governance teams |
| [FAQ](overview/faq.md) | Straight answers on pricing, data, permissions, and behavior |

## Architecture

For architects and security reviewers: how it's built and how data moves.

| Page | What you'll find |
|---|---|
| [Architecture overview](architecture/01-overview.md) | The components, and what runs in your tenant versus MAQ's |
| [Data flow](architecture/02-data-flow.md) | How telemetry, requests, and actions move end to end |
| [Fabric artifacts](architecture/03-fabric-artifacts.md) | Every item the agent creates in your workspace and what it's for |
| [Azure components](architecture/04-azure-components.md) | The resources deployed to your subscription and the identities they use |
| [Detection logic](architecture/05-detection-logic.md) | How Throttling Risk and Idle Capacity are detected |

## Setup

One-time installation. Follow the pages in order — each builds on the last.

| Page | What you'll do |
|---|---|
| [Prerequisites](setup/prerequisites.md) | Confirm roles, permissions, Azure access, and the Capacity Metrics App |
| [01 — Tenant settings](setup/01-tenant-settings.md) | Enable the settings that allow partner workloads |
| [02 — Deploy the workload](setup/02-deploy-workload.md) | Create the agent in your workspace and deploy its Fabric components |
| [03 — Deploy Azure resources](setup/03-deploy-azure.md) | Deploy the automation resources to your subscription |
| [04 — Permissions](setup/04-permissions.md) | Give the automation resources the access they need |
| [05 — Connections](setup/05-connections.md) | Authorize the agent's data connections |
| [06 — Azure OpenAI](setup/06-azure-openai-api-key.md) | (Optional) Connect Azure OpenAI for AI-written summaries |
| [07 — Email notifications](setup/07-email-notifications.md) | Set up the sending account for alerts |
| [08 — Scheduling](setup/08-pipeline-schedule.md) | Schedule the daily data load and insight generation |
| [09 — Verify deployment](setup/09-verify-deployment.md) | Confirm everything is working |

## Operations

Day-to-day use once you're up and running.

| Page | What you'll do |
|---|---|
| [Onboard a capacity](operations/onboard-a-capacity.md) | Start monitoring a capacity |
| [Configure findings](operations/configure-findings.md) | Tune sensitivity, thresholds, and AI Insights per capacity |
| [Autoscaling & scheduling](operations/autoscaling-and-scheduling.md) | Set monitoring windows, Auto-Scale limits, and pause/resume schedules |
| [Notifications](operations/notifications.md) | Choose who gets alerted, and mute during maintenance |
| [Troubleshooting](operations/troubleshooting.md) | Fix the most common issues, scenario by scenario |
| [Known limitations](operations/known-limitations.md) | What to expect around pauses, scaling steps, and data refresh |

## Reference

| Page | What you'll find |
|---|---|
| [Permissions matrix](reference/permissions-matrix.md) | Every role required, who needs it, and why |
| [Email settings](reference/smtp-settings.md) | Sending-account configuration |
| [Data tables](reference/kql-tables.md) | The tables the agent stores findings and history in |
| [Glossary](reference/glossary.md) | Terms used throughout the documentation |

## Compliance

| Page | What you'll find |
|---|---|
| [Workload attestation](compliance/attestation.md) | MAQ Software's attestation against Microsoft's Fabric workload publishing requirements |

---

## Deployment files

| File | Used in |
|---|---|
| [`deploy/ARM-FunctionApp-FAA.json`](../deploy/ARM-FunctionApp-FAA.json) | [Setup 03 — Deploy Azure resources](setup/03-deploy-azure.md) |

## Project

[README](../README.md) · [Release notes](../CHANGELOG.md) · [Support](../SUPPORT.md) · [Security](../SECURITY.md) · [License](../LICENSE)

Questions? **CustomerSuccess@MAQSoftware.com**
