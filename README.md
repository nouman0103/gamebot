<div align="center">

[![GameBot](https://img.shields.io/badge/Discord-Bot-5865F2?style=for-the-badge&logo=discord&logoColor=white)](https://discord.com/discovery/applications/717416553099952219)
![Python](https://img.shields.io/badge/Python-discord.py-3776AB?style=for-the-badge&logo=python&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-336791?style=for-the-badge&logo=postgresql&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-DC382D?style=for-the-badge&logo=redis&logoColor=white)

# GameBot

A Discord bot for [Krunker](https://krunker.io) that lets players link their in-game accounts and view detailed stat profiles directly in Discord.

[![Servers](https://img.shields.io/badge/Servers-9%2C400%2B-blueviolet?style=flat-square)](https://discord.com/discovery/applications/717416553099952219)
[![Linked Accounts](https://img.shields.io/badge/Linked%20Accounts-40%2C500%2B-blue?style=flat-square)](https://discord.com/discovery/applications/717416553099952219)
[![Since](https://img.shields.io/badge/Since-June%202020-green?style=flat-square)](https://discord.com/discovery/applications/717416553099952219)

</div>

---

## Overview

[GameBot](https://discord.com/discovery/applications/717416553099952219) is a Discord bot built for the Krunker community. Players link their Krunker accounts to Discord and can instantly retrieve rich, image-based stat cards covering kills, deaths, KDR, accuracy, W/L ratio, XP, playtime, and more.

Built and maintained solo since June 2020, the bot has grown to over 9,400 Discord servers and 40,500 linked user accounts.

---

## Features

- **Player Profiles** -- Link Krunker accounts to Discord and retrieve stat cards on demand
- **Stat Tracking** -- Kills, deaths, KDR, score, accuracy, W/L ratio, nukes, XP, playtime, maps, and modes
- **Image Generation** -- Stats rendered as formatted image cards and sent directly in Discord
- **Multi-account Support** -- Link multiple Krunker accounts per Discord user with a configurable main account

---

## Tech Stack

| Layer | Technology |
|---|---|
| Language | Python |
| Discord Library | discord.py |
| Database | PostgreSQL + Redis |
| Image Generation | Pillow / PIL |
| IPC | [Winerp](https://pypi.org/project/winerp/) |
| Infrastructure | Dedicated VPS |

---

## Architecture

The bot runs as two decoupled processes, both maintaining a direct connection to Discord via discord.py and communicating with each other through [Winerp](https://pypi.org/project/winerp/), a custom WebSocket IPC library built for this project.

```mermaid
graph TB
    D[Discord]
    MW["Main Wing<br/>Core Commands + Caching"]
    BW["Background Wing<br/>Personalization + Aux Commands"]
    R[(Redis)]
    PG[(PostgreSQL)]

    D <--> BW
    D <--> MW
    MW <-->|Winerp IPC| BW
    MW --> PG
    MW --> R
    BW --> R
```

The Main wing handles all core commands and is the only process with PostgreSQL access and caching enabled. The Background wing handles personalization and auxiliary commands in isolation, keeping non-critical logic out of the core path. discord.py acquires a lock on file sends that blocks the async event loop. Isolating image and GIF processing in a separate process with its own Discord connection ensures this lock never stalls core command handling in the Main Wing.

**Performance:**
- Image generation time reduced from 8s to 0.4s via a multithreaded processing pipeline (20x improvement)
- IPC response time under 2ms via Winerp
- Infrastructure managed on a self-hosted VPS

---

## Preview

<div align="center">
  <img src="assets/profile_card_1.png" alt="GameBot Profile Card" width="500" />
  <img src="assets/profile_card_2.png" alt="GameBot Profile Card" width="500" />
</div>

---

## Related

**[Winerp](https://github.com/nouman0103/winerp)** -- The open-source Python IPC library designed originally for this project. Published on PyPI with 50,000+ downloads and 13 GitHub stars.

---

## Author

**M. Nouman Iqbal**

[![GitHub](https://img.shields.io/badge/GitHub-nouman0103-black?style=flat-square&logo=github)](https://github.com/nouman0103)
[![Stack Overflow](https://img.shields.io/badge/Stack%20Overflow-7%2C300%2B%20rep-orange?style=flat-square&logo=stackoverflow)](https://stackoverflow.com/users/8321664/nouman)
