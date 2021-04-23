## Docker volume

### Mount windows network drive

```bash
docker volume create \
    --driver local \
    --opt type=cifs \
    --opt device=//<ip-of-share>/<path/to/mount> \
    --opt o=user=<username>,domain=<domain>,password=<pwd> \
    mediashare
```
