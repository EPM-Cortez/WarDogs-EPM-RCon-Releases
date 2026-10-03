# WarDogs EPM RCon releases

Public installers and compiled releases for the WAR DOGS administration panel. Application source stays in the private EPM-Cortez/WarDogs-EPM-RCon repository.

Containers are delivered through GitHub Container Registry:

- ghcr.io/epm-cortez/wardogs-epm-rcon-panel
- ghcr.io/epm-cortez/wardogs-epm-rcon-backend

A stable release provides signed metadata, immutable image references, installers, the deployment bundle, and the host updater. Candidate prereleases are for explicit testing and do not advance the stable update feed.

The first stable release is being prepared. Installer commands will become available here once its container and bootstrap checks have passed. Do not use an unpublished or unverified image as a production baseline.

Linux VPS installation uses the shell bootstrap. Windows Docker Desktop installation uses PowerShell with a WSL2 Linux distribution and Docker Desktop integration. Both download compiled images; owners do not need Git access, Node.js, or a .NET SDK.

Routine updates appear in the panel for explicitly registered installation administrators after a staged candidate is promoted to stable. Database state, key-ring protection and private installation settings persist across application updates.
