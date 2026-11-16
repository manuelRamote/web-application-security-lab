# Lab setup — OWASP Juice Shop and Burp Suite

## Environment observed

- Kali Linux virtual machine
- Burp Suite Community Edition 2026.8
- Docker service installed and started
- OWASP Juice Shop accessible from the Kali VM at `http://127.0.0.1:3000`

The precise Docker image digest/version and virtualization mode were not recorded. Capture them before the final project release to improve reproducibility.

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
