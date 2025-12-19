# OCI VM Watchdog (Binance Bot)

This repo runs a GitHub Actions workflow every 5 minutes to verify a health URL on your Oracle VM. If the health check fails, it triggers an OCI **SOFTRESET** on the instance.

## What it does
- **Health check**: `curl` a URL (dashboard root or `/api/health`).
- **Auto‑recover**: call OCI API to soft‑reset the VM if the URL is down.

## Required GitHub Secrets
Set these in **Repo → Settings → Secrets and variables → Actions → Secrets**:

- `OCI_USER_OCID`
- `OCI_TENANCY_OCID`
- `OCI_FINGERPRINT`
- `OCI_REGION` (e.g., `eu-frankfurt-1`)
- `OCI_INSTANCE_OCID`
- `OCI_API_KEY_PEM` (full private key contents)
- `HEALTH_URL` (e.g., `http://79.76.100.87/api/health?run_mode=live`)

## Workflow
- File: `.github/workflows/vm_watchdog.yml`
- Schedule: every 5 minutes
- Manual run: Actions → **VM Watchdog** → **Run workflow**

## Notes
- **SOFTRESET** is used to preserve the boot volume while rebooting the VM.
- The dashboard’s `/api/health` endpoint returns **200** when trader + candles are fresh, **503** when stale.
- The workflow installs `oci-cli` at runtime and writes a temporary config/key to the runner.

## Troubleshooting
- If the workflow fails at the OCI step, verify all secrets and that the API key is associated with your OCI user.
- If the health URL is reachable but services are down, the workflow will **not** reboot the VM. Consider a VM‑local watchdog for service restarts.
