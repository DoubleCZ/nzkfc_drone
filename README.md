# nzkfc_drone
A deployable drone resource for FiveM. Supports **Qbox**, **QBCore** and **ESX** frameworks. Players can deploy a personal drone that follows them, provides healing and can be flown manually in FPV mode.

Licensed under **GNU GPL v3** — free to use, modify and share. Commercial sale is prohibited.

---

## Preview

<a href="https://i.imgur.com/oMCiSsK.png" target="_blank"><img src="https://i.imgur.com/oMCiSsK.png" width="30%" alt="Drone deployed"></a>&nbsp;<a href="https://i.imgur.com/7zt3Pnm.png" target="_blank"><img src="https://i.imgur.com/7zt3Pnm.png" width="30%" alt="Drone FPV"></a>&nbsp;<a href="https://i.imgur.com/13ESXZk.jpeg" target="_blank"><img src="https://i.imgur.com/13ESXZk.jpeg" width="30%" alt="Drone aerial view"></a>

---

## Features
- **Deploy & Recall** — Pull out your drone with a kneel animation. The drone spawns on the ground in front of you and lifts off to your shoulder. Pack it away the same way.
- **Shoulder Follow** — When deployed, the drone hovers at your left shoulder and follows you smoothly with a natural bob and heading delay.
- **FPV Control** — Take manual control of the drone in first-person view. Fly freely up to a configurable range before signal is lost.
- **Healing Mode** — Activate healing to have the drone restore health to all players within a configurable radius. Shows a pulsing green AOE marker.
- **Drone Storage** — Each drone has its own persistent stash inventory. Store items, batteries and equipment. Drone items cannot be placed inside the stash.
- **Battery System** — The drone requires a `drone_battery` in its stash to operate. Battery drains over time. When depleted the drone lands and powers down until a new battery is inserted.
- **Damage System** — The drone can be shot down. Health degrades with hits and is displayed on each strike. Configurable max health.
- **Destruction & Recovery** — When destroyed the drone swaps to a wrecked model and falls to the ground. Storage remains accessible for a configurable time before being cleared.
- **Battery Removal** — Removing the battery mid-flight causes the drone to drop and power down. Reinserting a battery brings it back to life automatically.
- **Unique Serial Numbers** — Each drone is assigned a unique serial `DRN-XXXXXX` on first use, stored in item metadata. Visible in your inventory.
- **Collision Detection** — FPV control includes raycast-based collision detection to prevent flying through walls and terrain.
- **Spotlight** — A toggleable front-mounted spotlight, controllable from the target menu or via `L` in FPV mode. Angle, colour, brightness, distance and cone width are all configurable.
- **Native GTA Audio** — Uses GTA's built-in `DLC_BTL_Drone_Sounds` audio bank. No external sound files required.
- **Multi-Framework Support** — Auto-detects Qbox, QBCore and ESX at runtime. No manual framework configuration required.
- **Fully Configurable** — All behaviour, offsets, speeds, battery drain, healing settings, spotlight settings and more are in a single `config.lua`.

---

## Dependencies

| Resource | Notes |
|---|---|
| [ox_inventory](https://github.com/overextended/ox_inventory) | Inventory (2.45.0+) |
| [ox_lib](https://github.com/overextended/ox_lib) | UI notifications and animations |
| [ox_target](https://github.com/communityox/ox_target) | Drone interaction targeting — **must be the communityox fork** |
| [oxmysql](https://github.com/overextended/oxmysql) | Database (used by ox_inventory) |

### Framework (one of)

| Resource | Notes |
|---|---|
| [qbx_core](https://github.com/Qbox-project/qbx_core) | Qbox |
| [qb-core](https://github.com/qbcore-framework/qb-core) | QBCore |
| [es_extended](https://github.com/esx-framework/esx_core) | ESX |

> **ESX note:** ox_lib, ox_inventory, ox_target and oxmysql are not included in a standard ESX installation and must be added separately.

> **ox_target note:** This resource requires the [communityox fork](https://github.com/communityox/ox_target) of ox_target. The archived overextended version does not support the `disableTargeting` export used to prevent menu access while the player is downed.

---

## Installation

### 1. Add the resource

Drop the `nzkfc_drone` folder into your `resources` directory and add the following to your `server.cfg`:

```
ensure nzkfc_drone
```

### 2. Add items to ox_inventory

Open `ox_inventory/data/items.lua` and add the following entries:

```lua
-- nzkfc_drone
['drone'] = {
    label       = 'Drone',
    weight      = 2000,
    stack       = false,
    close       = true,
    consume     = 0,
    limit       = 1,
    description = 'A deployable drone.',
    client      = {
        event = 'nzkfc_drone:useItem'
    }
},
['drone_battery'] = {
    label       = 'Drone Battery',
    weight      = 300,
    stack       = false,
    close       = true,
    consume     = 0,
    description = 'Battery for a drone. Place inside the drone storage to power it.',
},
['drone_battery_empty'] = {
    label       = 'Empty Drone Battery',
    weight      = 300,
    stack       = false,
    close       = true,
    consume     = 0,
    description = 'Empty battery for a drone. Might be worth something?',
},
```

> **Note:** Use `event = 'nzkfc_drone:useItem'` rather than `export = 'nzkfc_drone.useDrone'` to avoid load order race conditions.

### 3. Add item images to ox_inventory

Place your item images in the `ox_inventory/web/images/` folder. Images must be **`.png`** format and named exactly after the item:

| File | Item |
|---|---|
| `drone.png` | Drone |
| `drone_battery.png` | Drone Battery |
| `drone_battery_empty.png` | Empty Drone Battery |

> If you don't have custom images, you can use any 128×128 PNG placeholder. ox_inventory will display a fallback icon if no image is found.

### 4. Give yourself a drone (testing)

Using an admin command:
```
/giveitem [playerid] drone 1
```

---

## Controls

### General

| Action | Control |
|---|---|
| Deploy / Pack away drone | Use the `drone` item from inventory |
| Recall drone (cancel stay) | `/calldrone` |

### Drone Target Menu (look at drone + interact key)

| Option | Description |
|---|---|
| **Drone Storage** | Open the drone's personal stash inventory |
| **Check Battery** | Display current battery percentage |
| **Guard Mode** | Shoots any player, NPC or animal that enters the configured radius |
| **Activate Healing** | Toggle healing aura for nearby players |
| **Take Control** | Enter FPV control mode |
| **Drone Flip** | Perform a 360° flip trick |
| **Tell Drone to Stay** | Park the drone at its current position |
| **Toggle Motor Sound** | Mute/unmute drone motor sounds |
| **Toggle Spotlight** | Turn the front-mounted spotlight on or off |

### FPV Control Mode

| Key | Action |
|---|---|
| `W` | Fly forward |
| `S` | Fly backward |
| `A` | Strafe left |
| `D` | Strafe right |
| `Q` | Ascend |
| `E` | Descend |
| `Mouse` | Look / Yaw |
| `L` | Toggle spotlight |
| `Space` | Disconnect and return to player |

---

## Configuration

All settings are in `shared/config.lua`. The framework is auto-detected at runtime — no manual configuration is required. To override the detected framework, uncomment and set the following line at the top of `config.lua`:

```lua
-- Config.Framework = 'qbx'  -- 'qbx', 'qbcore', or 'esx'
```

### Spotlight options

| Option | Default | Description |
|---|---|---|
| `Config.LightEnabled` | `true` | Enable/disable the spotlight feature entirely |
| `Config.LightR/G/B` | `255, 245, 200` | Beam colour (warm white) |
| `Config.LightDistance` | `25.0` | How far the beam reaches in metres |
| `Config.LightBrightness` | `5.0` | Beam intensity |
| `Config.LightRadius` | `20.0` | Cone width in degrees |
| `Config.LightFalloff` | `5.0` | Edge softness |
| `Config.LightAngle` | `45.0` | Downward tilt in degrees (0 = straight ahead, 90 = straight down) |

---

## License

Copyright (C) 2025 nzkfc

This program is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

See the [LICENSE](LICENSE) file for full details.
