# SSH Setup for GitHub & GitLab  
(Windows & Linux)

This guide explains how to set up SSH authentication for **GitHub** and **GitLab** using SSH keys.  
It includes **separate steps for Windows and Linux**, supported **key algorithms**, SSH agent setup, and best practices.

---

## Supported SSH Key Algorithms

| Algorithm | Status | Notes |
|---------|--------|------|
| **ED25519** | ✅ Recommended | Modern, fast, strong security |
| **RSA (4096)** | ✅ Supported | Use only if ED25519 is unavailable |
| ECDSA | ⚠️ Supported | Not recommended over ED25519 |
| DSA | ❌ Deprecated | Not supported |

---

# Windows Setup

## 1. Generate SSH Key
```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

Fallback:
```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

Default path:
```
C:\Users\<username>\.ssh\id_ed25519
```

---

## 2. Start SSH Agent (Admin PowerShell)
```powershell
Set-Service ssh-agent -StartupType Automatic
Start-Service ssh-agent
```

---

## 3. Add Key
ED25519 (recommended):
```powershell
ssh-add $env:USERPROFILE\.ssh\id_ed25519
```
RSA fallback:
```powershell
ssh-add $env:USERPROFILE\.ssh\id_rsa
```

---

## 4. SSH Config
Git Bash (nano):
```bash
nano ~/.ssh/config
```
Paste the block below, save with `Ctrl+O`, confirm with `Enter`, then exit with `Ctrl+X`.

PowerShell (Notepad):
```powershell
notepad $env:USERPROFILE\.ssh\config
```
Both commands edit the file stored at C:\Users\<username>\.ssh\config.
```text
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519
    IdentitiesOnly yes

Host gitlab.com
    HostName gitlab.com
    User git
    IdentityFile ~/.ssh/id_ed25519
    IdentitiesOnly yes
```

---

## 5. Upload Public Key
ED25519 (recommended):
```powershell
type $env:USERPROFILE\.ssh\id_ed25519.pub
```
RSA fallback:
```powershell
type $env:USERPROFILE\.ssh\id_rsa.pub
```

Upload to:
- GitHub → Settings → SSH and GPG keys
- GitLab → Preferences → SSH Keys

---

## 6. Test
```powershell
ssh -T git@github.com
ssh -T git@gitlab.com
```

---

# Linux Setup

## 1. Generate SSH Key


```bash
ssh-keygen -t ed25519 -C "<comment>"
```

```bash
ssh-keygen -t rsa -b 2048 -C "<comment>"
```


---

## 2. Start Agent
```bash
eval "$(ssh-agent -s)"
```

---

## 3. Add Key
ED25519 (recommended):
```bash
ssh-add ~/.ssh/id_ed25519
```
RSA fallback:
```bash
ssh-add ~/.ssh/id_rsa
```

---

## 4. SSH Config
```text
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519
    IdentitiesOnly yes

Host gitlab.com
    HostName gitlab.com
    User git
    IdentityFile ~/.ssh/id_ed25519
    IdentitiesOnly yes
```

```bash
chmod 600 ~/.ssh/config
```

---

## 5. Upload Public Key
```bash
cat ~/.ssh/id_ed25519.pub
```

---

## 6. Test
```bash
ssh -T git@github.com
ssh -T git@gitlab.com
```

---

## Notes
- SSH user is always **git**
- Never share private keys
- Use `ssh-add -l` to verify loaded keys
- Debug with `ssh -vT git@github.com`

---

## References
- https://docs.github.com/en/authentication/connecting-to-github-with-ssh
- https://docs.gitlab.com/user/ssh/
