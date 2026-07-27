# Publish This Project to GitHub

## Recommended repository settings

- Name: `opnsense-network-security-homelab`
- Visibility: Public, after one final privacy review
- Description: `Virtualized OPNsense firewall, IDS/IPS, NAT, DNS, policy validation, and isolated DVWA security testing lab.`
- Topics: `opnsense`, `suricata`, `ids`, `ips`, `firewall`, `homelab`, `dvwa`, `network-security`, `cybersecurity`

## Git command-line method

1. Extract the repository ZIP.
2. Open PowerShell or Git Bash inside the extracted folder.
3. Create an empty GitHub repository with the recommended name.
4. Run:

```bash
git init
git add .
git commit -m "Document OPNsense network security homelab"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/opnsense-network-security-homelab.git
git push -u origin main
```

## GitHub web-upload method

1. Create a new empty repository.
2. Choose **uploading an existing file**.
3. Drag the contents of the extracted project folder into the browser. Upload the contents, not the parent ZIP.
4. Commit the files to `main`.
5. Check every image and Markdown link from the repository homepage.

## Before making it public

- Search the project for your personal email address and company names.
- Open every screenshot at full size.
- Remove cookies, tokens, passwords, public IPs, and unrelated bookmarks.
- Confirm DVWA is no longer reachable from any public or bridged interface.
- Add the repository URL to the resume only after the README renders correctly.
