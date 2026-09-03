# ![logo](https://raw.githubusercontent.com/azerothcore/azerothcore.github.io/master/images/logo-github.png) AzerothCore Module: mod-cfbg-enhanced

[![AzerothCore Module](https://img.shields.io/badge/AzerothCore-Module-red?style=flat-square&logo=github)](https://github.com/azerothcore/azerothcore-wotlk)
[![C++20](https://img.shields.io/badge/Language-C++20-00599C?style=flat-square&logo=c%2B%2B)](https://isocpp.org/)
[![Branch 3.3.5a](https://img.shields.io/badge/Branch-3.3.5a-orange?style=flat-square)](https://github.com/azerothcore/azerothcore-wotlk)
[![License MIT](https://img.shields.io/badge/License-MIT-blue?style=flat-square)](https://opensource.org/licenses/MIT)

An advanced cross-faction battleground matchmaking, party team-locking, and gear balancing module for **AzerothCore (WotLK 3.3.5a)**.

### 💡 Why this module?
The standard cross-faction battleground module frequently breaks parties apart just to balance player headcounts. The entire point of queueing in a group is to play together with your friends, not be forced to fight against them. 

**`mod-cfbg-enhanced`** solves this by treating queued groups as atomic units to guarantee that party members always stay on the same team, while balancing matches fairly using players' actual item levels rather than blind numbers.

## 📊 Feature Comparison

| Feature | Standard CFBG | mod-cfbg-enhanced |
| :--- | :---: | :---: |
| **Party Integrity (`GroupTeamLock`)** | ❌ Separates queued groups to fill slots | ✅ **Guaranteed:** Queued parties remain on the same team |
| **Gear Balance (`Average Item Level`)** | ❌ Blind headcount only | ✅ **Calculates aggregate team iLvl for balanced matchups** |
| **Wintergrasp Battlefield Support** | ❌ Unsupported | ✅ **Full cross-faction queue, native priority, and war locks** |
| **Low-Level Class Balancing** | ❌ None | ✅ **Distributes dominant classes evenly across brackets (e.g. 10-19)** |
| **Racial Morph Engine** | ❌ Generic morph IDs | ✅ **Class-valid WotLK mappings preserving animations and audio** |
| **Session Team Locking** | ❌ Re-rollable by leaving/rejoining | ✅ **Persistent team assignment lock per match cycle** |
| **Live Entry Rebalancing** | ❌ Risk of splitting premades | ✅ **Rebalances solo joiners while keeping premades intact** |

## ⚙️ Technical Architecture

### 1. Group Queue Atomicity & Team Locking
Standard CFBG implementations evaluate queue slots individually, which frequently separates queued parties when filling battleground brackets or during live-entry rebalancing passes.

`mod-cfbg-enhanced` encapsulates queued groups within `CrossFactionGroupInfo`:
- Preserves party structure during `BattlegroundQueue` assignment.
- Locks assigned team IDs across all party members through invitation and teleport phases (`CFBG.GroupTeamLock.Enabled = 1`).
- Rebalances solo entries when teams are uneven without modifying queued group assignments.

### 2. Item Level & Rating Balancing
Calculates aggregate gear score (`AveragePlayersItemLevel`) and level metrics for each queue entry:
- Evaluates `SumAverageItemLevel` across both sides before initializing the match instance.
- Distributes premades and solo players according to composite team strength rather than blind headcounts.

### 3. Wintergrasp & World Battlefield Integration
Extends cross-faction logic to `Battlefield` (Wintergrasp):
- **Native Priority:** Prioritizes keeping the minority faction on its native side, only morphing the surplus players of the majority faction.
- **War Lock:** Enforces persistent team locks per battle cycle, preventing faction re-rolling via zone exit/re-entry or relogging.
- **Resurrection Persistence:** Automatically re-applies faction templates and morph auras on spirit resurrection.

### 4. Class-Valid Racial Morphing
Handles visual morphing and client-side faction template masking:
- Maps players to valid WotLK race-class combinations via `RaceData` to prevent animation glitches and missing audio assets.
- Preserves native racial spells and passive auras during morphing.

## 📋 Configuration Reference (`CFBG.conf`)

| Setting | Default | Description |
| :--- | :---: | :--- |
| `CFBG.Enable` | `1` | Enables the cross-faction battleground matchmaking system. |
| `CFBG.GroupTeamLock.Enabled` | `1` | Keeps queued group members on the same battleground team. |
| `CFBG.Include.Avg.Ilvl.Enable` | `1` | Balances teams using players' average item level. |
| `CFBG.EvenTeams.Enabled` | `1` | Enforces strict N vs N team parity before matches start. |
| `CFBG.Players.Count.In.Group` | `3` | Maximum allowed party size for battleground group queue. |
| `CFBG.Battlefield.Enable` | `1` | Enables cross-faction matchmaking in Wintergrasp. |
| `CFBG.Battlefield.TeamLock.Enable` | `1` | Locks players to their assigned team for the Wintergrasp battle. |
| `CFBG.Battlefield.NativePriority.Enable` | `1` | Keeps the minority faction native and morphs majority surplus. |
| `CFBG.BalancedTeams.Class.LowLevel` | `0` | Balances dominant classes (e.g. Hunters) in twink brackets. |

## 💬 In-Game Commands

| Command | Security Level | Description |
| :--- | :---: | :--- |
| `.cfbg status` | Player | Displays active matchmaking state and queue stats. |
| `.cfbg enable` | Admin (Level 3) | Enables the CFBG matchmaking system dynamically. |
| `.cfbg disable` | Admin (Level 3) | Disables the CFBG matchmaking system dynamically. |
| `.cfbg race <race>` | Player | Sets preferred morph race when assigned to opposite faction. |

## 🛠️ Installation

1. Place the module in `azerothcore-wotlk/modules/`:
   ```bash
   cd azerothcore-wotlk/modules
   git clone https://github.com/AlsoNotMehh/mod-cfbg-enhanced.git
   ```

2. Re-run CMake and compile your server:
   ```bash
   cmake -B build
   cmake --build build --config Release
   ```

3. Set `Battleground.InvitationType = 0` in `worldserver.conf`.
4. Copy `conf/CFBG.conf.dist` to your `worldserver` configs directory as `CFBG.conf` and customize as needed.

## ⭐ Show your support

If you find this module helpful for your server, please consider giving it a star on GitHub! It helps more developers in the AzerothCore community discover the project.

## 🤝 Credits

- **Author & Enhancements:** [AlsoNotMehh](https://github.com/AlsoNotMehh) ([Discord](https://discord.com/users/1063304041419001966) / [Email](mailto:itsbrayanrodriguez@gmail.com))
- **Original Base Work:** [Winfidonarleyan](https://github.com/Winfidonarleyan), [Viste](https://github.com/Viste), [Irancore](https://github.com/Irancore)
- **Framework:** [AzerothCore](https://www.azerothcore.org)

## 📜 License

This project is licensed under the [MIT License](LICENSE).
