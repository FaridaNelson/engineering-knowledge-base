# OpenClaw / PulseForge Operations

## Start VM

```bash
gcloud compute instances start <OPENCLAW_VM_NAME> --zone=<ZONE>
```

## Connect

```bash
gcloud compute ssh <OPENCLAW_VM_NAME> --zone=<ZONE>
```

Expected prompt:

```text
<username>@<OPENCLAW_VM_NAME>:~$
```

## OpenClaw runtime

```bash
cd ~/projects/openclaw
```

## Verify services

```bash
docker compose ps
```

Expected:

```
openclaw-openclaw-cli-1       healthy
openclaw-openclaw-gateway-1   healthy
```

## Restart

```bash
docker compose down
docker compose up -d
```

## Logs

```bash
docker compose logs --tail=50 openclaw-gateway
```

## Stop VM when finished

```bash
exit

gcloud compute instances stop openclaw-dev --zone=us-west1-b
```
