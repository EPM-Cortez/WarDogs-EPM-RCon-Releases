# WarDogs EPM RCon releases

Public installers and compiled releases for the WAR DOGS administration panel and API. This repository distributes the RCon application only. Application source stays in the private EPM-Cortez/WarDogs-EPM-RCon repository.

Containers are delivered through GitHub Container Registry:

- ghcr.io/epm-cortez/wardogs-epm-rcon-panel
- ghcr.io/epm-cortez/wardogs-epm-rcon-backend

The panel image contains the compiled API and administration frontend. The backend image contains the compiled API only.

A stable release provides signed metadata, immutable image references, installers, the deployment bundle, and the host updater. Candidate prereleases are for explicit testing and do not advance the stable update feed.

The commands below install the latest signed stable release from [Releases](https://github.com/EPM-Cortez/WarDogs-EPM-RCon-Releases/releases).

Linux VPS installation uses the shell bootstrap. Windows Docker Desktop installation uses PowerShell with a WSL2 Linux distribution and Docker Desktop integration. Both download compiled images; owners do not need Git access, Node.js, or a .NET SDK.

Routine updates appear in the panel for explicitly registered installation administrators after a staged candidate is promoted to stable. Database state, key-ring protection and private installation settings persist across application updates.

## Linux VPS

Use an x64 Ubuntu 22.04/24.04 or Debian 12/13 VPS with systemd. Point a DNS hostname at it and allow TCP 80/443. These ports and the default application installation must be unused; existing web servers and WarDogs data are preserved by refusal.

Replace the hostname, then run:

```bash
curl --fail --location --proto '=https' --proto-redir '=https' \
  https://github.com/EPM-Cortez/WarDogs-EPM-RCon-Releases/releases/latest/download/bootstrap-wardogs.sh \
  -o bootstrap-wardogs.sh && sudo bash bootstrap-wardogs.sh --hostname admin.example.com
```

The launcher installs missing native tools/Docker from the official apt repository, verifies signed payload and images, creates private secrets, then asks for your owner email, community name and password. It installs the panel with HTTPS, PostgreSQL and its managed updater. Open the printed HTTPS URL, sign in and enrol an authenticator. Initial passwords require 14+ characters, upper/lowercase letters, a number and a symbol.

## Windows Docker Desktop

Use x64 Windows, Docker Desktop's Linux containers/WSL 2 backend, and an existing supported Ubuntu/Debian WSL 2 distribution with systemd. Enable Docker Desktop's WSL integration for that distribution. Do not install another Docker Engine inside WSL. The installer checks these settings and does not reboot Windows or restart WSL.

Run in PowerShell, replacing `Ubuntu` if your distribution has another name (for example `Ubuntu-24.04`):

```powershell
$installer = Join-Path $env:TEMP ('Install-WarDogs-' + [guid]::NewGuid().ToString('N') + '.ps1')
Invoke-WebRequest -UseBasicParsing -Uri 'https://github.com/EPM-Cortez/WarDogs-EPM-RCon-Releases/releases/latest/download/Install-WarDogs.ps1' -OutFile $installer
powershell.exe -NoProfile -ExecutionPolicy Bypass -File $installer -Distribution Ubuntu
```

The panel opens at `https://localhost` and its private data lives inside WSL. The installer exports its validated public HTTPS CA certificate to a protected file in `%LOCALAPPDATA%\WarDogsEPMRCon` and prints its path. Add `-TrustLocalCertificate` to explicitly trust that certificate for your current Windows user, or import it manually. The default user logon task keeps WSL active; `-NoLogonTask` opts out. Docker Desktop must remain running. Enable its start-at-sign-in setting if you want it after a Windows restart.

[Docker Desktop WSL setup](https://docs.docker.com/desktop/features/wsl/) and [WSL systemd setup](https://learn.microsoft.com/en-us/windows/wsl/systemd) describe the prerequisites. Automated launcher checks do not replace acceptance on a real disposable Windows host.

## Updates and backups

The first owner is registered as the installation update administrator. The panel's **Download now** fetches missing image layers while the panel stays online. **Install update** performs maintenance, an encrypted backup, migrations/grants and application replacement; the browser reconnects after readiness checks. Game-server processes remain independent.

Do not rerun the initial installer for an upgrade: it refuses existing files, projects and occupied ports. If installation fails partway through, preserve its files/secrets and review the failed step. Routine updates preserve PostgreSQL, HTTPS, the protecting key ring and private configuration; helper/host software upgrades require operator maintenance.

Privately back up installation secrets, database/key-ring volumes and updater state. Keep `/etc/wardogs-updater/backup-key` separately from encrypted backups. The default installation is `/opt/wardogs-admin`; use `sudo systemctl status wardogs-updater` and `sudo journalctl -u wardogs-updater --since today` for updater diagnostics.

Candidate prereleases require an exact `vMAJOR.MINOR.PATCH-candidate.RUN_ID.RUN_ATTEMPT` tag plus `--allow-candidate` on Linux or `-AllowCandidate` on Windows. Download the launcher from that exact candidate rather than `/latest`. Candidates expire after seven days and belong on disposable test hosts; stable owners see an update only after explicit promotion.
