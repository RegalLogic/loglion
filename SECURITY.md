# Security

Log Lion opens files and connects to services that may come from untrusted sources, so we
take reports of security vulnerabilities seriously.

## Reporting a vulnerability

**Please don't report security vulnerabilities in public issues or discussions.**

Instead, either:

- use GitHub's [private vulnerability reporting](https://github.com/RegalLogic/loglion/security/advisories/new), or
- email [contact@regallogic.com](mailto:contact@regallogic.com) with "Security" in the subject.

Please include the Log Lion version (**Help ▸ About**), the steps or file that trigger the
problem, and what an attacker could achieve. A minimal file that reproduces it helps most;
we'll tell you how to send it privately.

We'll acknowledge your report within a few business days, keep you informed as we work on
a fix, and credit you in the release notes if you'd like.

## Supported versions

Fixes are made in the latest release of Log Lion. The Microsoft Store keeps installs up to
date automatically; with the MSI installer, install the current release.

## What's in scope

- Log Lion itself, as installed from the Microsoft Store or with the MSI installer.
- The extensions published by Regal Logic: Docker, Splunk and Grafana MCP.

Extensions from anyone else are the responsibility of their authors. Log Lion asks before
loading an extension that isn't signed by Regal Logic, and shows its publisher.
