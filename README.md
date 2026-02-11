# Kubernetes KVM Backup

This repository contains the Kubernetes manifests and configuration templates for a **production backup setup**.

The backup solution runs as a Kubernetes workload and performs **SSH-based backups** from a remote KVM host to persistent storage inside the cluster.

No secrets or environment-specific credentials are stored in this repository.

---

## Scope

The repository focuses on the **infrastructure layer** of the backup system:

- Kubernetes PersistentVolume and PersistentVolumeClaim definitions
- Kubernetes Pods / CronJob templates used for SSH connectivity and backup execution
- Anonymized configuration templates suitable for public sharing

Operational details such as credentials, hostnames, ports, and secrets are intentionally excluded.

---

## Architecture Overview

- A Kubernetes cluster hosts the backup workload
- Persistent storage is provided via a Kubernetes PersistentVolume
- The backup pod connects to a KVM host via SSH (read-only access)
- Database dumps are created on the KVM host by a separate cron job and included in the backup
- Monitoring and alerting are handled externally (e.g. via Grafana)

This repository documents **how the infrastructure is wired**, not the confidential runtime configuration.

---

## Repository Contents

| File | Description |
|------|------------|
| `pv.yaml` | PersistentVolume definition for backup storage |
| `pvc.yaml` | PersistentVolumeClaim used by the backup workload |
| `restic-init-job.yaml` | One-time Kubernetes Job used to initialize the restic repository |
| `restic-backup-test-job.yaml` | Anonymized test job for validating SSH-based backups |
| `restic-backup-cronjob.yaml` | Production-ready CronJob template for scheduled restic backups |
| `ssh-test-pod.yaml` | Anonymized test pod for validating SSH connectivity |

---

## Security Notes

- No private keys, passwords, or tokens are committed
- SSH keys, hostnames, ports, and credentials are provided via Kubernetes Secrets outside this repository
- Local or productive manifests should use separate, ignored files (e.g. `*.local.yaml`)

See `.gitignore` for excluded files.

---

## Anonymization Notice

All hostnames, usernames, ports and environment-specific configuration values in this repository have been anonymized. The manifests provided here are structural templates and do not expose any production credentials or sensitive infrastructure details.

---

## Usage

This repository is intended as a **reference and operational baseline**.

Before applying manifests in a production environment:
- Replace placeholders with environment-specific values
- Provide required credentials via Kubernetes Secrets
- Review storage paths and access permissions

---

## Status

This project is currently under active development.
Once finalized, it will be used in a productive environment.
The repository is updated continuously as the backup setup evolves.
