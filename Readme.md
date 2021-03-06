# sLimits

[![Build Status](https://github.com/Silthus/sLimits/workflows/Build/badge.svg)](../../actions?query=workflow%3ABuild)
[![GitHub release (latest SemVer including pre-releases)](https://img.shields.io/github/v/release/Silthus/sLimits?include_prereleases&label=release)](../../releases)
![Spiget tested server versions](https://img.shields.io/spiget/tested-versions/78922)
[![Spiget Downloads](https://img.shields.io/spiget/downloads/78922)](https://www.spigotmc.org/resources/slimits.78922/)
[![Spiget Rating](https://img.shields.io/spiget/rating/78922)](https://www.spigotmc.org/resources/slimits.78922/)
[![Commitizen friendly](https://img.shields.io/badge/commitizen-friendly-brightgreen.svg)](http://commitizen.github.io/cz-cli/)
[![semantic-release](https://img.shields.io/badge/%20%20%F0%9F%93%A6%F0%9F%9A%80-semantic--release-e10079.svg)](https://github.com/semantic-release/semantic-release)

[![sLimits Splash Screen](docs/img/slimits_splash_small.png)](https://www.spigotmc.org/resources/slimits.78922/)

- [Features](#features)
- [Installation](#installation)
- [Configuration](#configuration)
  - [Limit Modes](#limit-modes)
- [Commands](#commands)
  - [Player Commands](#player-commands)
  - [Admin Commands](#admin-commands)
- [Permissions](#permissions)
  - [Admin Permissions](#admin-permissions)

## Features

The plugin currently has the following features. Please [open a feature request](https://github.com/Silthus/sLimits/issues/new?assignees=&labels=&template=feature_request.md&title=) if you want a feature added.

- **Limiting block placement** based on configs inside `limits/`.
- Keeps **track** of the **placed blocks** and destroying them decreases the limit counter.
- Player data is **stored in flat files** inside the `storage/` directory.
- **Unique permissions** per limit config. Only if a player has that permission the limits of the config are checked.
- **Combine multiple configs** with different modes: `ADD`, `SUBTRACT` and `ABSOLUTE`.
- **Chest UI** to show your limits and placed block locations.
- **Block destruction** of your limited blocks by other players.

## Installation

Simply drop the plugin into your `plugins` folder and restart your server.

## Configuration

There is a main `config.yaml` in the plugins directory which holds some global options.

```yaml
# The type of storage to use.
# Currently only supports: FLATFILES
storage: FLATFILES
# A path where to store your data in.
# Must be a relative path based of your plugins data directory.
storage_path: storage
# You can define some block limit defaults here
block_config:
    # Set to false when you do not want to decrease the limit counter
    # if someone else destroys a limited block played by a player.
    delete_blocks_destroyed_by_others: true
    # Set to true if you want to completely block destruction
    # of limited blocks by other players than the one who placed the block.
    block_limited_block_destruction: false
```

You can configure your limits by creating a limit config inside the `limits/` directory.
Currently the config only supports a `blocks` section where you can define the blocks and their amount that players are allowed to place.

> **Note:** The name of the block type must be in UPPERCASE.

```yaml
# Define the limit config mode. See below for details.
mode: ADD
# Optional: override the default permission with your own.
# Remove the line or keep it as an empty string to use the default permission (see below).
permission: ''
# List your blocks that you want to limit.
blocks:
    DIRT: 10
    BEDROCK: 5
```

### Limit Modes

You can specify three different limit modes in your configs: `ADD`, `SUBTRACT`, `ABSOLUTE`

> The limit mode is only relevant if you have multiple configs that define the same block type
> and players that have multiple limit permissions for those configs.

The limit mode defines the rules how multiple configs with the same block type play together.

| Mode | Description |
| :---: | ---------- |
| `ADD` | **Default** - If a block type is defined in multiple configs the limits are summed up. |
| `SUBTRACT` | Subtracts the limits in this config from the sum of the `ADD` configs. |
| `ABSOLUTE` | If a player get's assigned an absolute config. Only that config will apply. Scoped to the block types defined in that config. |

## Commands

You can use the following commands to manage your limits.

### Player Commands

| Command | Description | Permission |
| :-----: | ----------- | ------- |
| `/limits [player]` | Shows the limits of the current or another player. | none |
| `/limits show [player]` | Shows the limits of the current or another player in a chest gui. | none |
| `/limits loc <block_type>` | Shows all block locations of a certain type. | none |

### Admin Commands

| Command | Description | Permission |
| :-----: | ----------- | ------- |
| `/limits reload` | Reloads all configs from disk and clears the cache. Saves everything before reloading | `slimits.admin.reload` |

## Permissions

For each limit config, permissions are automatically generated and only players with the given permission of the config are assigned that limit.
The permission is constructed from the full path to the config, including its name, separated by dots.

Take the following examples:

| Config File | Permission |
| ----------- | ---------- |
| `limits/my-limit.yaml` | `slimits.limit.my-limit` |
| `limits/subfolder/sub-sub-dir/my-limit.yaml` | `slimits.limit.subfolder.sub-sub-dir.my-limit`

### Admin Permissions

There are the following admin permissions.

| Permission | Description |
| ---------- | ----------- |
| `slimits.admin.exclude` | Excludes the player from being assigned to any limits. Overrules any limit permission the player may have. |
