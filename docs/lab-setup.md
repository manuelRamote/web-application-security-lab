# Lab setup — OWASP Juice Shop and Burp Suite

## Environment observed

- Kali Linux virtual machine
- Burp Suite Community Edition 2026.8
- Docker service installed and started
- OWASP Juice Shop accessible from the Kali VM at `http://127.0.0.1:3000`

The precise Docker image digest/version and virtualization mode were not recorded. Capture them from the original Kali VM before public release to improve reproducibility. These values cannot be reconstructed reliably from the screenshots.

## Record the exact Docker image version before release

When the original container is still present, use these commands on the Kali VM:

```bash
sudo docker inspect juice-shop-lab --format 'Image ID: {{.Image}}; Configured image: {{.Config.Image}}'
sudo docker image inspect bkimminich/juice-shop:latest --format 'Repo digests: {{json .RepoDigests}}; ID: {{.Id}}'
```

Copy the observed immutable digest or image ID into this documentation. The initial test container was started from `bkimminich/juice-shop` (implicitly `latest`); **do not label an unknown release as a specific version**. The second command may fail if the tag is absent; in that case use the image ID returned by `docker inspect`. These commands do not change the running container.

## Start the local application

```bash
sudo systemctl is-active docker
sudo docker ps
```

If the lab container is absent and port 3000 is free, run:

```bash
sudo docker run --rm -d \
  --name juice-shop-lab \
  -p 127.0.0.1:3000:3000 \
  bkimminich/juice-shop
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
