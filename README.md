# 🎮 Linux Gaming Configuration

This document describes the gaming environment configured on Debian, including GameMode, MangoHud, GE-Proton and per-game launch options for Steam.


## 🖥️ System environment

The following system configuration applies to the documented game installations:

| Component | Configuration |
|---|---|
| Operating system | Debian 13.8 |
| Desktop environment | GNOME 48 |
| Display server | Wayland |
| Kernel | [Linux 7.3.6](https://github.com/azagramac/linux-kernel/releases/tag/kernel-135) |
| Gaming platform | Steam |
| Compatibility layer | GE-Proton |
| Performance overlay | MangoHud |
| Performance management | GameMode |

## 📦 GameMode & MangoHud

[GameMode](https://github.com/FeralInteractive/gamemode) is a daemon developed by Feral Interactive that allows games to request temporary system optimisations while running, such as CPU governor adjustments, process prioritisation and other configurable performance settings.

### Installation

Install GameMode and MangoHud:

```bash
sudo apt install -y gamemode mangohud
```

### Verify GameMode service

GameMode runs as a systemd user service. Check its status with:

```bash
systemctl --user status gamemoded
```

An inactive service is not necessarily an error:

```text
○ gamemoded.service - gamemoded
     Loaded: loaded (/usr/lib/systemd/user/gamemoded.service; disabled; preset: enabled)
     Active: inactive (dead)
```

GameMode normally starts automatically when a game requests it through `gamemoderun`. It does not need to remain active permanently.

To start the daemon manually for testing:

```bash
systemctl --user start gamemoded
```

Verify that it is running:

```bash
systemctl --user status gamemoded
```

Expected status:

```text
● gamemoded.service - gamemoded
     Loaded: loaded (/usr/lib/systemd/user/gamemoded.service; disabled; preset: enabled)
     Active: active (running)
     Status: "GameMode is ready to be activated."
```

### Run GameMode diagnostics

Execute the built-in test suite:

```bash
gamemoded -t
```

Expected result:

```text
: Loading config
Loading config file [/usr/share/gamemode/gamemode.ini]
: Running tests

:: Basic client tests
:: Passed

:: Dual client tests
gamemode request succeeded and is active
Quitting by request...
:: Passed

:: Gamemoderun and reaper thread tests
...Waiting for child to quit...
...Waiting for reaper thread (reaper_frequency set to 5 seconds)...
:: Passed

:: Supervisor tests
:: Passed

:: Feature tests
::: Verifying CPU governor setting
::: Passed
::: Verifying Scripts
::: Passed (no scripts configured to run)
::: Verifying GPU Optimisations
::: Passed (gpu optimisations not configured to run)
::: Verifying renice
::: Passed (no renice configured)
::: Verifying ioprio
::: Passed
:: Passed

: All Tests Passed!
```

A successful test confirms that GameMode is installed and its tested functionality is operational.

---

## ⚠️ Troubleshooting: CPU governor verification

On modern AMD systems using `amd-pstate-epp`, GameMode may fail its CPU governor test even when the daemon itself is working correctly.

Typical error:

```text
::: Verifying CPU governor setting
ERROR: Governor was not set to performance (was actually powersave)!
::: Failed!
```

This can happen when another power-management service controls the CPU frequency policy.

### Identify the CPU frequency driver

Check the active scaling driver:

```bash
cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_driver
```

Expected on systems using the modern AMD P-State driver:

```text
amd-pstate-epp
```

Check the available CPU governors:

```bash
cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_available_governors
```

Example:

```text
performance powersave
```

### Check power-profiles-daemon

Debian systems may use `power-profiles-daemon` to manage CPU power policies.

Check its service status:

```bash
systemctl status power-profiles-daemon --no-pager
```

Example:

```text
● power-profiles-daemon.service - Power Profiles daemon
     Loaded: loaded (/usr/lib/systemd/system/power-profiles-daemon.service; enabled; preset: enabled)
     Active: active (running)
```

Check the current power profile:

```bash
powerprofilesctl get
```

Typical output:

```text
balanced
```

With the balanced profile, the CPU may use:

```text
scaling governor: powersave
```

This is normal behaviour for `amd-pstate-epp`. The `powersave` governor does not necessarily mean that the CPU is running at a permanently reduced frequency.

### Reproduce the test using the performance profile

Temporarily switch to the performance profile:

```bash
powerprofilesctl set performance
```

Verify the selected profile:

```bash
powerprofilesctl get
```

Expected:

```text
performance
```

Check the active CPU governor:

```bash
cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_governor
```

Expected:

```text
performance
```

Run the GameMode diagnostics again:

```bash
gamemoded -t
```

If the test now finishes with:

```text
: All Tests Passed!
```

GameMode's CPU governor verification is working under the performance profile.

**Important:** This test does not establish that GameMode can override every external power-management policy. It only verifies the behaviour under the selected profile.

### Restore the balanced profile

After testing, restore the normal power-management configuration:

```bash
powerprofilesctl set balanced
```

Verify:

```bash
powerprofilesctl get
```

Expected:

```text
balanced
```

The CPU governor will normally return to:

```text
powersave
```

This is expected behaviour with `amd-pstate-epp`.

---

## 🚀 Steam configuration

### Enable GameMode

To enable GameMode for a Steam game, add the following to its Launch Options:

```bash
gamemoderun %command%
```

This requests GameMode when the game starts and releases the request when it exits.

### MangoHud

MangoHud provides an in-game performance overlay with monitoring information such as FPS, frame times, CPU/GPU utilisation, temperatures and power consumption.

Enable it through the launch option:

```bash
MANGOHUD=1 %command%
```

Both tools can be enabled together:

```bash
MANGOHUD=1 gamemoderun %command%
```

<img width="800" alt="GameMode Steam launch options" src="https://github.com/user-attachments/assets/22ec28b3-3e2f-4ab2-ac19-610c451dce6b" />

---

## 🧩 GE-Proton

[GE-Proton](https://github.com/GloriousEggroll/proton-ge-custom) is a community-maintained Proton build that includes additional patches and compatibility improvements for running Windows games on Linux.

### Download and installation

Download the desired release:

[GloriousEggroll / proton-ge-custom – Releases](https://github.com/GloriousEggroll/proton-ge-custom/releases)

Extract the downloaded `GE-ProtonXX-XX.tar.gz` archive and move the extracted directory into:

```bash
~/.local/share/Steam/compatibilitytools.d/
```

Create the directory if it does not exist:

```bash
mkdir -p ~/.local/share/Steam/compatibilitytools.d/
```

Restart Steam so that the new compatibility tool becomes available.

### Activate GE-Proton in Steam

Open the game's properties and navigate to:

**Properties → Compatibility → Force the use of a specific Steam Play compatibility tool.**

Select the installed GE-Proton version.

<img width="842" height="601" alt="Steam compatibility settings" src="https://github.com/user-attachments/assets/3a65ff71-dd12-42c7-a216-ab7872073523" />

---

# 🎮 Game configurations

The following sections document the tested Proton versions, launch parameters and graphics settings for each game.

## Grand Theft Auto V Legacy

<img width="460" alt="Grand Theft Auto V Legacy" src="https://github.com/user-attachments/assets/65b39e8a-7651-41f7-8e62-91db687993c0" />

**Configuration**

- Proton: [GE-Proton11-14](https://github.com/GloriousEggroll/proton-ge-custom/releases/tag/GE-Proton11-14)
- Launch options:

```bash
PROTON_ENABLE_WAYLAND=1 WAYLANDDRV_RAWINPUT=0 PROTON_FORCE_HDR=1 PROTON_NO_ESYNC=1 PROTON_USE_FSYNC=1 MANGOHUD=1 gamemoderun %command%
```

**Graphics settings**

<img width="800" alt="GTA V settings 1" src="https://github.com/user-attachments/assets/3603a0bb-1a18-4ba3-9f5f-99fa039a48b5" />

<img width="800" alt="GTA V settings 2" src="https://github.com/user-attachments/assets/3b3e96e8-f07a-4100-ba13-2abca2d333eb" />

<img width="800" alt="GTA V settings 3" src="https://github.com/user-attachments/assets/df9bb970-e4f8-4a99-b172-55270013ba0b" />

<img width="800" alt="GTA V settings 4" src="https://github.com/user-attachments/assets/2a1c6185-0470-4794-9f8c-a07d1cf86d00" />

**Game:** [Grand Theft Auto V Legacy – Steam](https://store.steampowered.com/app/271590/Grand_Theft_Auto_V_Legacy)

---

## Metal Gear Solid V: The Phantom Pain

<img width="460" height="215" alt="Metal Gear Solid V" src="https://github.com/user-attachments/assets/204a6ea3-bbd6-4e4a-8a0f-2fd7f87476c1" />

**Configuration**

- Proton: [GE-Proton11-14](https://github.com/GloriousEggroll/proton-ge-custom/releases/tag/GE-Proton11-14)
- Launch options:

```bash
WINE_FULLSCREEN_FSR=1 WINE_FULLSCREEN_FSR_STRENGTH=2 MANGOHUD=1 gamemoderun %command%
```

**Graphics settings**

<img width="800" alt="MGSV settings" src="https://github.com/user-attachments/assets/1931797a-6f08-4374-ae0f-9f33d2a1b499" />

**Game:** [METAL GEAR SOLID V: THE PHANTOM PAIN – Steam](https://store.steampowered.com/app/287700/METAL_GEAR_SOLID_V_THE_PHANTOM_PAIN/)

---

## Metal Gear Solid 3: Snake Eater – Master Collection

<img width="460" alt="Metal Gear Solid 3" src="https://github.com/user-attachments/assets/88dfb696-a475-405c-aef1-1cccad75f266" />

**Configuration**

- Proton: [GE-Proton11-14](https://github.com/GloriousEggroll/proton-ge-custom/releases/tag/GE-Proton11-14)
- Launch options:

```bash
WINE_FULLSCREEN_FSR=1 WINE_FULLSCREEN_FSR_MODE=quality WINE_FULLSCREEN_FSR_CUSTOM_MODE=1920x1080 %command%
```

**Compatibility note**

Adding the following parameters causes the controller to stop working in-game:

```bash
RADV_PERFTEST=aco PROTON_ENABLE_WAYLAND=1
```

These parameters are therefore excluded from this game's configuration.

**Graphics settings**

<img width="800" alt="MGS3 settings" src="https://github.com/user-attachments/assets/429ca661-4b28-4be6-b250-23bb5609cbf3" />

**Game:** [METAL GEAR SOLID 3: Snake Eater – Steam](https://store.steampowered.com/app/2131650/METAL_GEAR_SOLID_3_Snake_Eater__Master_Collection_Version/)

**Collection:** [METAL GEAR SOLID: MASTER COLLECTION Vol.1](https://store.steampowered.com/sub/886315/)

---

## Mortal Kombat 11 Ultimate

<img width="460" height="215" alt="Mortal Kombat 11" src="https://github.com/user-attachments/assets/2ce8cf5f-e5d7-46c9-87a5-ac33454f59a3" />

**Configuration**

- Proton: [GE-Proton11-14](https://github.com/GloriousEggroll/proton-ge-custom/releases/tag/GE-Proton11-14)
- Launch options:

```bash
WINE_FULLSCREEN_FSR=1 WINE_FULLSCREEN_FSR_MODE=quality WINE_FULLSCREEN_FSR_CUSTOM_MODE=1920x1080 DXVK_ASYNC=1 PROTON_FSR3_UPGRADE=1 RADV_PERFTEST=aco PROTON_ENABLE_WAYLAND=1 gamemoderun %command%
```

**Graphics settings**

<img width="800" alt="Mortal Kombat 11 settings" src="https://github.com/user-attachments/assets/889988b1-055d-459d-b05c-3c1dd32946d7" />

**Game:** [Mortal Kombat 11 – Steam](https://store.steampowered.com/app/976310/Mortal_Kombat_11/)

---

## Resident Evil 2

<img width="460" alt="Resident Evil 2" src="https://github.com/user-attachments/assets/8806a787-0343-4574-b65d-740a77449cb2" />

**Configuration**

- Proton: [GE-Proton11-14](https://github.com/GloriousEggroll/proton-ge-custom/releases/tag/GE-Proton11-14)
- Launch options:

```bash
WINE_FULLSCREEN_FSR=1 WINE_FULLSCREEN_FSR_MODE=quality WINE_FULLSCREEN_FSR_CUSTOM_MODE=1920x1080 DXVK_ASYNC=1 PROTON_FSR3_UPGRADE=1 RADV_PERFTEST=aco PROTON_ENABLE_WAYLAND=1 gamemoderun %command%
```

**Graphics settings**

<img width="800" alt="Resident Evil 2 settings 1" src="https://github.com/user-attachments/assets/8a47cc5f-9c2a-47b1-85ea-c75314c485ce" />

<img width="800" alt="Resident Evil 2 settings 2" src="https://github.com/user-attachments/assets/7559cf50-57b2-4161-a545-ed5472b5da10" />

<img width="800" alt="Resident Evil 2 settings 3" src="https://github.com/user-attachments/assets/66ead638-fc3f-4fc1-911d-332cc5692198" />

<img width="800" alt="Resident Evil 2 settings 4" src="https://github.com/user-attachments/assets/c1690aea-be41-4cc2-9f8c-a07d1cf3682b" />

**Game:** [Resident Evil 2 – Steam](https://store.steampowered.com/app/883710/Resident_Evil_2/)

---

## Resident Evil 3

<img width="460" alt="Resident Evil 3" src="https://github.com/user-attachments/assets/4d4e5417-2446-49c6-9ebb-3d787360997c" />

**Configuration**

- Proton: [GE-Proton11-14](https://github.com/GloriousEggroll/proton-ge-custom/releases/tag/GE-Proton11-14)
- Launch options:

```bash
WINE_FULLSCREEN_FSR=1 WINE_FULLSCREEN_FSR_MODE=quality WINE_FULLSCREEN_FSR_CUSTOM_MODE=1920x1080 DXVK_ASYNC=1 PROTON_FSR3_UPGRADE=1 RADV_PERFTEST=aco PROTON_ENABLE_WAYLAND=1 gamemoderun %command%
```

**Graphics settings**

<img width="800" alt="Resident Evil 3 settings 1" src="https://github.com/user-attachments/assets/24feb964-1baf-42d8-b03d-5607ca0602f6" />

<img width="800" alt="Resident Evil 3 settings 2" src="https://github.com/user-attachments/assets/dfb43a10-6343-477f-88e4-c70793971fd9" />

<img width="800" alt="Resident Evil 3 settings 3" src="https://github.com/user-attachments/assets/a4153e4e-a5bc-411d-b06b-00dce3f20100" />

<img width="800" alt="Resident Evil 3 settings 4" src="https://github.com/user-attachments/assets/492fc473-19ee-4d72-9452-2bea07370202" />

**Game:** [Resident Evil 3 – Steam](https://store.steampowered.com/app/952060/Resident_Evil_3/)

---

## Resident Evil 4

<img width="460" alt="Resident Evil 4" src="https://github.com/user-attachments/assets/b9684cbc-66c5-4c4b-a8e5-605d36e9351d" />

**Configuration**

- Proton: [GE-Proton11-14](https://github.com/GloriousEggroll/proton-ge-custom/releases/tag/GE-Proton11-14)
- Launch options:

```bash
WINE_FULLSCREEN_FSR=1 WINE_FULLSCREEN_FSR_MODE=quality WINE_FULLSCREEN_FSR_CUSTOM_MODE=1920x1080 DXVK_ASYNC=1 PROTON_FSR3_UPGRADE=1 RADV_PERFTEST=aco PROTON_ENABLE_WAYLAND=1 gamemoderun %command%
```

**Graphics settings**

<img width="800" alt="Resident Evil 4 settings 1" src="https://github.com/user-attachments/assets/fd001073-efbc-42e3-8d70-483d01fd23e4" />

<img width="800" alt="Resident Evil 4 settings 2" src="https://github.com/user-attachments/assets/86ec03f8-07d5-4f59-9c81-8704b842fe01" />

<img width="800" alt="Resident Evil 4 settings 3" src="https://github.com/user-attachments/assets/2473c4ae-6a4c-4b1f-8167-5ee4cebac23e" />

<img width="800" alt="Resident Evil 4 settings 4" src="https://github.com/user-attachments/assets/9eac92b0-1b6d-48d3-b554-d9c91df3682b" />

**Game:** [Resident Evil 4 – Steam](https://store.steampowered.com/app/2050650/Resident_Evil_4/)
