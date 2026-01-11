# DDS: Distributed Actor-Model MMORPG Server Architecture

[![MIT License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![AGPLv3 Recommendation](https://img.shields.io/badge/Core-AGPLv3-red.svg)](https://www.gnu.org/licenses/agpl-3.0.en.html)

> A modern, scalable, game-agnostic blueprint for MMORPG server emulators using Rust + the Actor model.  
> Memory-safe, crash-isolated, horizontally scalable — designed to fix the pain points of legacy C++ monoliths.

**DDS** (Distributed Systems Server) is **not** a working server emulator — it is a **theoretical design document** and **reference architecture** for building the next generation of MMORPG servers.

### Why DDS Exists
Legacy emulators (AzerothCore, TrinityCore, LandSandBoat, Kawari, etc.) are incredible community achievements, but they are built on 15–20-year-old foundations:
- C++ shared mutable state → frequent crashes, data races, memory bugs
- Mutex-heavy concurrency → limited scaling (500–1k players max on single node)
- Game-specific silos → duplicated effort across WoW, FFXI, EQ, L2, etc.
- Lua scripting everywhere → exploits, performance cliffs in concurrent scenarios

DDS flips this:
- **Rust** for zero-cost safety and fearless concurrency
- **Actor model** (ractor-inspired) for true isolation, supervision trees, and no shared state
- **Game-agnostic core** — only packets, DB schema, and Rhai blueprints change per game
- **Valkey hot cache** + PostgreSQL write-behind for real-time performance
- Single beefy Linux box → 2k+ players; horizontal scaling later

### Core Philosophy
- 70–80% of code (actors, supervisors, tick engine, session management, admin tools) can be **shared** across any MMORPG.
- Content creators focus only on game-specific parts — massive time savings.
- Single binary + Docker-first deployment — consistent, reproducible, no "it works on my machine."
- Fault tolerance built-in: supervision restarts, limp mode on DB/cache failure, grace periods for zones.

### Current Status
🚧 **Design document only** — no implementation exists yet.  
I am not a programmer and have no plans to build this myself. This is shared freely as inspiration. Rust developers, emulator enthusiasts, or teams tired of C++ crashes — fork it, build it, improve it.

### Quick Comparison to Legacy Emulators

| Feature                        | Legacy (C++/TrinityCore/AzerothCore/LSB) | DDS (Rust + Actor Model)                     |
|--------------------------------|------------------------------------------|----------------------------------------------|
| Memory Safety                  | Manual, prone to crashes/races           | Guaranteed by Rust ownership                 |
| Concurrency                    | Mutexes, limited threading               | Actor isolation — no races, supervision      |
| Scaling                        | Vertical (better hardware)               | Single-node 2k+, horizontal later            |
| Game Agnosticism               | Game/version silos                       | 80% shared core, only packets/Rhai/DB change |
| Fault Tolerance                | Crash → restart whole server             | Actor restarts, limp mode, write-behind      |
| Scripting                      | Lua (exploits, races in MT)              | Rhai (safe, Rust-native, config-only)        |
| Deployment                     | Varies, often messy                      | Linux + Docker only — consistent environment |

### Scaling & Optimization Notes (Key Highlights)
- Single-node first: 64+ cores handles thousands via Tokio + lightweight actors
- Zone Supervisor can be sharded + parented by a Hypervisor at extreme scale
- Spatial/proximity optimization (prune/sleep/hybrid) configurable per-zone via Rhai
- Grace periods for empty instances: configurable (no delay for instanced on wipe/complete)
- Tick Engine for deterministic, synchronized timing across all actors

### Licensing Recommendations
This **design document** is licensed under the **MIT License** — copy, adapt, build on it freely.

**Any actual server implementation** (the runnable code) should be released under the **GNU AGPLv3** to ensure modifications and improvements are shared back with the community, especially for public/networked servers. See [Why I Hate Most WoW Private Servers](https://github.com/FrancescoBorzi/why-I-hate-wow-private-servers/blob/master/ENGLISH.md) for why this matters in the emulator space. Game-specific content (DB schemas, Rhai scripts) can be proprietary/ARR if needed for commercial use.

### Get Involved
- Star this repo to stay updated.
- Open discussions/issues with feedback, critiques, or questions.
- If you're a Rust developer interested in building a proof-of-concept (even for one game), fork away — I'd love to link to real implementations here.
- No pressure, no ego — just ideas that live forever.

Godspeed, developers.  
Let's build the server emulators the scene deserves.

[Full Design Document → dds-design.md](./dds-design.md)
