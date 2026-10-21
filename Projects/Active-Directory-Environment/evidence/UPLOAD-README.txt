# Santi CyberLab — Active Directory evidence

These screenshots come from the user's lab walkthroughs and are cropped for readability (content unchanged).
They are ready for uploading into `Projects/Active-Directory-Environment/evidence/`.

## Files

- **`01-dc01-static-ip-dns.png`** — DC01 IPv4 and DNS configuration. Original: `Screenshot 2026-10-21 155448.png`.
- **`02-dc01-ad-dns-roles.png`** — AD DS and DNS roles visible in Server Manager. Original: `Screenshot 2026-10-21 155811.png`.
- **`03-dc01-domain-promotion.png`** — New forest deployment for corp.santicyber.test. Original: `Screenshot 2026-10-21 155715.png`.
- **`04-dc01-diagnostics.png`** — DC locator and dcdiag /test:dns verification. Original: `Screenshot 2026-10-21 155951.png`.
- **`05-client01-network-settings.png`** — CLIENT01 IPv4 192.168.56.20 and DNS 192.168.56.10. Original: `Screenshot 2026-10-21 160050.png`.
- **`06-client01-connectivity-dns.png`** — ipconfig, ping, domain DNS, LDAP SRV records. Original: `Screenshot 2026-10-21 160309.png`.
- **`07-client01-domain-join-success.png`** — Windows Welcome to domain message. Original: `Screenshot 2026-10-21 160342.png`.
- **`08-client01-ad-computer-object.png`** — CLIENT01 account in ADUC Computers container. Original: `Screenshot 2026-10-21 160429.png`.
- **`09-client01-secure-channel.png`** — Test-ComputerSecureChannel True. Original: `Screenshot 2026-10-21 160443.png`.
- **`10-sally-morganx-account-creation.png`** — Fictional employee Sally MorganX smorgan account creation. Original: `Screenshot 2026-10-21 160512.png`.
- **`11-sally-domain-logon-verified.png`** — whoami corp\smorgan; logon server \\DC01 (from earlier shared screenshot). Original: `image-1792570982876.jpg`.

## What to do
1. Extract this ZIP on your own computer.
2. In the GitHub `Home-IT-Lab` repository, open `Projects/Active-Directory-Environment/evidence/`.
3. Choose **Add file -> Upload files**, drag the 11 PNG files into the upload area, then **Commit changes**.
4. Keep the original unedited screenshots and recordings in your local backup.
5. Ask ChatGPT to update the main project README to display these images in a curated evidence section.

The final `whoami`/`LOGONSERVER` image is based on the earlier photo shared in the same conversation; if you have a crisp native screenshot in the video, prefer replacing this with that image.
There are no employee passwords or authentication secrets visible in the selected crops, but always review screenshots before publishing publicly.
