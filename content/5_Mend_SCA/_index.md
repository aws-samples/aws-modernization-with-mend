---
title: "Mend SCA - Identify Security Vulnerabilities and Licenses"
chapter: true
weight: 5
---

# Mend SCA - Identify Security Vulnerabilities and Licenses

## Overview
**Mend SCA** integrations allow you to automate the software composition analysis (SCA) of your software project, tapping into the continuous integration pipeline.  
The integration also allows you to automatically fail builds or trigger other actions in case of a security or compliance policy violation.  

**Mend SCA** can be integrated with any build/CI tool or any other tool that enables the execution of shell commands.  
In this workshop, we will cover the integration of **Mend SCA** with [AWS CodeBuild](https://aws.amazon.com/codebuild/), scanning a software project that's hosted on [AWS CodeCommit](https://aws.amazon.com/codecommit/).

### What is SCA?
Software Composition Analysis (SCA) is an automated process that detects and identifies open-source software in a codebase. This analysis is mainly performed to evaluate the overall security posture of that codebase, as well as its license compliance, as companies need to be aware of open-source license limitations and obligations.

Software Composition Analysis provides three main core capabilities:

- Build a software bill of materials (SBOM) to establish a detailed inventory of your open-source software packages
- Verify license compliance requirements by determining which open-source software components you’re using and where they originated
- Discover detailed information about key vulnerabilities in your source code and provide applicable remediation suggestions

<hr>
