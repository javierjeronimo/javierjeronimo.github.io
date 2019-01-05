---
title: 'Notes on article: Towards Secure Cloud Orchestration for Multi-Cloud Deployments'
categories:
  - arquitectura
---

Notes on article: 'Towards Secure Cloud Orchestration for Multi-Cloud Deployments'
==

These are just some personal notes on the article: http://doi.acm.org/10.1145/3195870.3195874

---

Assumptions
--
 - Hardware integrity
 - Physical Security
 - Low-Level Software Stack
 - Cryptographic Security
 - Availability

Adversary Capabilities
--
 - Network Infrastructure <== network is not trusted

High Level Attacks
--
 - VM Substitution Attack: start vulnerable VMs
 - Host Substitution Attack: bypass VM placement policies.
 - Storage Host Substitution Attack: bypass storage placement policies.
 - Resource Parasite Attack: bypass infrastructure configuration policies, e.g. for running proccesses.
 - Placement Bias Attack: bypass placement policies in federated environments.

Orchestration Security Enablers
--
 - Crypto Engine
 - Credential Manager
 - Firewall Service
 - PKI Manager
 - Attestation Service: Image Integrity Verifier, Image Delta Verifier
