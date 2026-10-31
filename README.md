# UniFi Network Application (Portainer-ready)

MongoDB 4.4 plus the LinuxServer UniFi Network Application. No bind mounts or local files are needed, so you can deploy it straight from Git.

## Deploy in Portainer

1. Go to **Stacks → Add stack → Repository**.
2. Enter the repository URL and reference (e.g. `refs/heads/main`).
3. Set the compose path to `docker-compose.yml`.
4. Add the environment variables below under **Environment variables**.
5. Click **Deploy the stack**.

The UI is at `https://<host>:8443`.

## Environment variables

### Mandatory

| Variable    | Description                                                            |
|-------------|------------------------------------------------------------------------|
| `MONGO_PASS`| Password for the MongoDB user. The deploy fails if it is not set.      |

### Optional (defaults shown)

| Variable           | Default      | Description                                    |
|--------------------|--------------|------------------------------------------------|
| `MONGO_USER`       | `unifi`      | MongoDB username                               |
| `MONGO_DBNAME`     | `unifi`      | Database name (`<name>_stat` is created too)   |
| `PUID`             | `1000`       | User ID the app runs as                        |
| `PGID`             | `1000`       | Group ID the app runs as                       |
| `TZ`               | `Etc/UTC`    | Timezone, e.g. `Europe/Athens`                 |
| `MEM_LIMIT`        | `1024`       | Max Java heap (MB)                             |
| `MEM_STARTUP`      | `1024`       | Initial Java heap (MB)                         |
| `UNIFI_TAG`        | `latest`     | Image tag for the UniFi container              |
| `MONGO_TLS`        | *(empty)*    | Set to use TLS for Mongo                       |
| `MONGO_AUTHSOURCE` | *(empty)*    | Mongo auth source                              |

## Volumes

- `unifi-db-data`: MongoDB data
- `unifi-config`: UniFi configuration and backups

## Ports

| Port        | Purpose                              |
|-------------|--------------------------------------|
| 8443/tcp    | Web UI                               |
| 8080/tcp    | Device inform                        |
| 3478/udp    | STUN                                 |
| 10001/udp   | Device discovery                     |
| 1900/udp    | L2 discovery (optional)              |
| 8843/tcp    | Guest portal HTTPS (optional)        |
| 8880/tcp    | Guest portal HTTP (optional)         |
| 6789/tcp    | Speed test (optional)                |
| 5514/udp    | Remote syslog (optional)             |

## Notes

- The Mongo user is created only on first start, when the data volume is empty. Changing `MONGO_PASS` later does not change the Mongo user's password. Change it inside Mongo, or delete the `unifi-db-data` volume.
- `docker-compose-controller.yml` is the legacy standalone controller stack. It needs no mandatory variables and uses the volume `unifi-controller-config`.
- To migrate existing data from `app_data/`, copy it into the new volumes before first start.