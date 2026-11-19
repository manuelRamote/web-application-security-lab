# Lab setup — OWASP Juice Shop and Burp Suite

## Environment observed

- Kali Linux virtual machine
- Burp Suite Community Edition 2026.8
- Docker service installed and started
- OWASP Juice Shop accessible from the Kali VM at `http://127.0.0.1:3000`

**Image provenance verified on the original Kali VM (19 November 2026):**

| Item | Observed value |
| --- | --- |
| Container | `juice-shop-lab` |
| Container status | Running (reported uptime: 3 days) |
| Configured image | `bkimminich/juice-shop` (unversioned; implicitly `latest`) |
| Host port binding | `127.0.0.1:3000->3000/tcp` |
| Docker image ID | `sha256:634032bbcc50e4d2096734bc6a984265973c966db13933354dc85a9218d54836` |
| Repository digest | `bkimminich/juice-shop@sha256:877fc77b12d72b42e486eb09b6b1711cc56514306d92006b284e7e7cf076a809` |

The **same image ID** was returned by both `docker inspect juice-shop-lab` and `docker image inspect bkimminich/juice-shop:latest`; therefore the locally running container matches the image whose repository digest was inspected. This immutable image reference allows the lab environment to be reproduced more consistently than `latest`. **The application's semantic release version and hypervisor mode remain unverified**; do not infer either from the digest.

## Verify image provenance

The following **read-only** commands were run on the original Kali VM:

```bash
sudo docker ps -a
sudo docker inspect juice-shop-lab --format 'Image: {{.Config.Image}} | Image ID: {{.Image}}'
sudo docker image inspect bkimminich/juice-shop:latest --format 'RepoDigests: {{json .RepoDigests}} | Image ID: {{.Id}}'
```

Both inspection commands reported the same immutable image ID, and the latter reported the repository digest given above. This records **image provenance**, not a verified Juice Shop semantic version number. No Docker image update is required to repeat the original tests.

## Start the local application

```bash
sudo systemctl is-active docker
sudo docker ps
```

For a **new, separate lab** only, if the lab container is absent and port 3000 is free, run the pinned image below. **Do not run this over the current active lab or stop the existing container**:

```bash
sudo docker run --rm -d \
  --name juice-shop-lab \
  -p 127.0.0.1:3000:3000 \
  bkimminich/juice-shop@sha256:877fc77b12d72b42e486eb09b6b1711cc56514306d92006b284e7e7cf076a809
```

Check:

```bash
sudo docker ps
curl -I http://127.0.0.1:3000
```

The host binding to `127.0.0.1` prevents exposure on ordinary external interfaces of the Kali VM. Do not publish this intentionally vulnerable app on a public address.

## Start Burp Suite

1. Run `burpsuite` in Kali.
2. Select **Community Edition → Temporary project in memory → Use Burp defaults**.
3. In **Proxy → Intercept**, use **Open browser**.
4. Set **Intercept off** for ordinary browsing; Burp still records requests in **Proxy → HTTP history**.
5. Browse to `http://127.0.0.1:3000` with Burp's integrated browser.
6. Send individual requests to **Repeater** for controlled manual tests.

## Capture controls

- Use only your own local target and two dedicated test accounts.
- Keep raw tokens, cookies, passwords/hashes, and personal details out of the repository.
- Record request method, URL, status code and limited response attributes without reproducing secrets.
- Never upload unredacted Proxy history exports.

## End the session

```bash
sudo docker stop juice-shop-lab
```

Because this example uses `--rm`, stopping the container removes the container and its writable filesystem. **Do not run this command if you intend to preserve test accounts/data and have not recorded the state or designed persistent storage.** A VM snapshot can preserve the current running environment separately.
