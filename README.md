# BANCOS-PY-capture-data

Captura automática del snapshot de Meltwater del reporte **Monitoreo Bancos - Itaú**
(Benchmark Bancos · Paraguay) y envío al pipeline NSGroup.

```
cron-job.org → GitHub Actions (capture_meltwater.py baja y valida el .gz)
  → webhook de Make (BANCOS-PY-DATA)
  → HTTP POST /update-url {"url":"...","sync":true} + x-sync-token
  → Cloudflare Worker (monitorbancospy) → KV (BANCOSMONITOR, key latest_bancos_py)
  → Dashboard /monitor-bancos
```

## Secrets del repo (Settings → Secrets and variables → Actions)

| Secret | Valor |
|---|---|
| `MELTWATER_URL` | URL **pública** del snapshot del reporte Bancos (`app.meltwater.com/report/public/<ID>/insight-pages/snapshot?snapshot_token=...`) |
| `MELTWATER_PASSWORD` | Contraseña del reporte público (si tiene gate); default `NSG` |
| `MAKE_WEBHOOK_URL` | `https://hook.us2.make.com/2jprr2qcj8cwxjplh15hnoohvxq9op34` |

## Disparo (cron-job.org, cada 30 min)

`POST https://api.github.com/repos/Eyewatch4U/BANCOS-PY-capture-data/actions/workflows/meltwater.yml/dispatches`
Body: `{"ref":"main"}` · Header: `Authorization: Bearer <PAT Actions RW>`
