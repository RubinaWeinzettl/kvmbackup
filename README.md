# Kubernetes KVM Backup Architecture

This repository documents a Kubernetes-based backup architecture designed to protect multiple Docker Compose applications running on a KVM host.

Backups are executed as Kubernetes workloads and retrieve data via SSH from a remote server. Data is stored in an encrypted restic repository.

All configuration files in this repository are anonymized. No credentials or sensitive infrastructure details are included.

---

## Development Phase (Test Cluster)

The initial implementation was developed independently on a test cluster.

During this phase:

- A small local PersistentVolume was created within the cluster for testing purposes
- The restic repository initialization workflow was implemented
- SSH key-based authentication was configured
- Backup CronJobs were built and validated
- Test snapshots were successfully created and verified

The implementation was carried out independently with technical support from AI tools.

The manifests included in this repository reflect this test-cluster setup.

---

## Production Deployment

The production rollout was performed in a collaborative sparring setup.

In production:

- The restic repository is stored on an NFS-backed PersistentVolume
- The NFS share resides on a NAS system
- Operational manifests are maintained in a private GitLab repository
- Deployment is performed via ArgoCD (GitOps)

During deployment, several issues were identified and resolved:

- Missing NFS target directory
- Unreplaced placeholder values in configuration templates
- StorageClass mismatch between PersistentVolume and PersistentVolumeClaim

---

## Architecture Overview

- Kubernetes CronJobs execute scheduled backups
- Separate CronJobs per application directory
- SSH key-based authentication to the KVM host
- Encrypted restic repository
- Retention management using `restic forget --prune`
- GitOps deployment via ArgoCD

---

## Current Backup Strategy

At the moment, directories are streamed via `tar` over SSH into restic using stdin.

Example concept:
ssh remote "tar -cf - /path" | restic backup --stdin


This approach is stable and simple. However, restic sees only a single archive file instead of individual files.

As a result:

- Any change inside the archive (e.g. a new database dump)
  causes the entire archive to be stored again
- File-level deduplication is not fully utilized
- Restore operations extract the archive as a whole

---

## Planned Improvement

The backup process will be refactored to pass files directly to restic instead of wrapping them in a tar archive.

This will enable:

- Proper file-level deduplication
- More efficient incremental backups
- Reduced storage consumption
- More granular restore capabilities

This change aligns the implementation more closely with restic’s design principles and improves long-term efficiency.

---

## Security

- All hostnames, usernames, ports and environment-specific values are anonymized
- No private keys or credentials are stored in this repository
- Production secrets are managed via Kubernetes Secret resources

---

## Status

The backup architecture is deployed and operational in production.
This repository represents the anonymized test-cluster implementation.

