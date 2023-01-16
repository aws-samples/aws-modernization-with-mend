---
title: "Mend Renovate - Keep Your Dependencies Up-To-Date"
chapter: true
weight: 15
---

# Mend Renovate - Keep Your Dependencies Up-To-Date

## Overview
Mend Renovate keeps source code dependencies up-to-date using automated Pull Requests.  

It scans your repositories to detect package files and their dependencies, checks if any newer versions exist and raises Pull Requests for available updates.  
The Pull Requests patch the package files directly, and include Release Notes for the newer versions (if they are available).  

### Why Use Mend Renovate?
- Get automated Pull Requests to update your dependencies
- Reduce noise by running Mend Renovate on a schedule (e.g. on weekends, outside of working hours, daily/weekly, etc.)
- Relevant package files are discovered automatically
- Supports monorepo architectures (e.g. Lerna or Yarn workspaces) with no additional configuration
- Bot behavior is customizable via configuration files (config as code) or via environment variables
- Use ESLint-like shared config presets for ease of use and simplifying configuration (JSON format only)
- Lock files are supported and updated in the same commit, including immediately resolving conflicts whenever PRs are merged
- Get replacement PRs to migrate from a deprecated dependency to the community suggested replacement (npm packages only)
- Open source (installable via npm/Yarn or Docker Hub) so can be self-hosted or used via GitHub App

<hr>
