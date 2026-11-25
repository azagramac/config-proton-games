# 🎮 GameMode

[GameMode](https://github.com/FeralInteractive/gamemode) permite optimizar temporalmente el sistema cuando se ejecutan juegos, por ejemplo ajustando el governor de CPU.

## 📦 Install GameMode & mangohud

Install the package:

```bash
sudo apt install -y gamemode mangohud
```

🔍 Check GameMode service
GameMode runs as a user service, so use systemctl --user:
```bash
systemctl --user status gamemoded
```
output

```bash
○ gamemoded.service - gamemoded
     Loaded: loaded (/usr/lib/systemd/user/gamemoded.service; disabled; preset: enabled)
     Active: inactive (dead)
```
> This is not necessarily an error.


`gamemoded` is normally started on demand when a client requests GameMode. It does not need to be permanently running.
To start it manually for testing:
```bash
systemctl --user start gamemoded
```
Then verify:
```bash
systemctl --user status gamemoded
```

output expected:
```bash
● gamemoded.service - gamemoded
     Loaded: loaded (/usr/lib/systemd/user/gamemoded.service; disabled; preset: enabled)
     Active: active (running) since Sat 2026-08-29 16:57:53 CEST; 2s ago
 Invocation: 3cadad69d5724633b14e8dc393d43e0d
   Main PID: 14120 (gamemoded)
     Status: "GameMode is ready to be activated."
      Tasks: 2 (limit: 76860)
     Memory: 916K (peak: 2M)
        CPU: 6ms
     CGroup: /user.slice/user-1000.slice/user@1000.service/app.slice/gamemoded.service
             └─14120 /usr/bin/gamemoded
```

## 🧪 Run GameMode tests

Run:
```bash
gamemoded -t
```
output expected:
```bash
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
> ✅ GameMode is correctly installed and working.


## ⚠️ Troubleshooting: CPU governor test fails

On systems using modern AMD CPUs with amd-pstate-epp, GameMode may report:

```bash
::: Verifying CPU governor setting
ERROR: Governor was not set to performance (was actually powersave)!
::: Failed!
```
> This does not necessarily mean that GameMode is broken.

First check which CPU frequency driver is being used:
```bash
cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_driver
```
For an AMD system using the modern P-State driver, the result should be similar to:
```bash
amd-pstate-epp
```
Check the available governors:
```bash
cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_available_governors
```
output expected:
```bash
performance powersave
```

## 🔋 Check power-profiles-daemon
Debian may have power-profiles-daemon enabled:
```bash
systemctl status power-profiles-daemon --no-pager
```
output expected:
```bash
$ systemctl status power-profiles-daemon --no-pager
● power-profiles-daemon.service - Power Profiles daemon
     Loaded: loaded (/usr/lib/systemd/system/power-profiles-daemon.service; enabled; preset: enabled)
     Active: active (running) since Sat 2026-08-29 16:37:41 CEST; 39min ago
 Invocation: 97f8018f198049109c31e307f1e06c18
   Main PID: 1515 (power-profiles-)
      Tasks: 4 (limit: 76860)
     Memory: 1.7M (peak: 2.6M)
        CPU: 135ms
     CGroup: /system.slice/power-profiles-daemon.service
             └─1515 /usr/libexec/power-profiles-daemon

Warning: some journal files were not opened due to insufficient permissions.
```
Check the current power profile:
```bash
powerprofilesctl get
```
output expected:
```bash
balanced
```
the CPU may be using:
```bash
scaling governor: powersave
```

❌ Output error Gamemode
```bash
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
ERROR: Governor was not set to performance (was actually powersave)!
::: Failed!
::: Verifying Scripts
::: Passed (no scripts configured to run)
::: Verifying GPU Optimisations
::: Passed (gpu optimisations not configured to run)
::: Verifying renice
::: Passed (no renice configured)
::: Verifying ioprio
::: Passed
ERROR: :: Failed!
: Tests Failed!
```
> ⚠️ This can cause the GameMode CPU governor test to fail.


## 🚀 Test with the performance profile
Temporarily switch to the performance profile:
```bash
powerprofilesctl set performance
```
Verify:
```bash
powerprofilesctl get
```
Expected:
```bash
performance
```
Then check the CPU governor:
```bash
cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_governor
```
Expected:
```bash
performance
```
Run the GameMode test again:
If the test now ends with:
```bash
: All Tests Passed!
```
> ✅ GameMode is correctly installed and working.

## 🔄 Restore the normal profile
After testing, return to the normal balanced profile:
```bash
powerprofilesctl set balanced
```
Verify:
```bash
powerprofilesctl get
```
Expected:
```bash
balanced
```
The CPU governor will normally return to:
```bash
powersave
```
This is expected.

## 🕹️ Test in game

🎮 Using GameMode with games
For Steam games, add this to the game's Launch Options:
```bash
gamemoderun %command%
```

<img width="800" alt="test" src="https://github.com/user-attachments/assets/22ec28b3-3e2f-4ab2-ac19-610c451dce6b" />

## 🚀 Install GE-Proton

### ⬇️ Download latest version
🔗 https://github.com/GloriousEggroll/proton-ge-custom/releases

- Uncompress file `GE-ProtonXX-XX.tar.gz`
- Copy extracted folder to:
  ```bash
  ~/.local/share/Steam/compatibilitytools.d/
  ```

## ⚙️ Activate GE-Proton on Steam
<img width="842" height="601" alt="image" src="https://github.com/user-attachments/assets/3a65ff71-dd12-42c7-a216-ab7872073523" />

## Grand Theft Auto V Legacy
<img width="460" alt="img_gta" src="https://github.com/user-attachments/assets/65b39e8a-7651-41f7-8e62-91db687993c0" />

- Proton version: [GE-Proton11-14](https://github.com/GloriousEggroll/proton-ge-custom/releases/tag/GE-Proton11-14)
- Command:
  ```bash
  PROTON_ENABLE_WAYLAND=1 WAYLANDDRV_RAWINPUT=0 PROTON_FORCE_HDR=1 PROTON_NO_ESYNC=1 PROTON_USE_FSYNC=1 MANGOHUD=1 gamemoderun %command%
  ```
- Settings game:
<img width="800" alt="settings_01" src="https://github.com/user-attachments/assets/3603a0bb-1a18-4ba3-9f5f-99fa039a48b5" />
<img width="800" alt="settings_02" src="https://github.com/user-attachments/assets/3b3e96e8-f07a-4100-ba13-2abca2d333eb" />
<img width="800" alt="settings_03" src="https://github.com/user-attachments/assets/df9bb970-e4f8-4a99-b172-55270013ba0b" />
<img width="800" alt="settings_04" src="https://github.com/user-attachments/assets/2a1c6185-0470-4794-9f8c-a07d1cf86d00" />


- Link: https://store.steampowered.com/app/271590/Grand_Theft_Auto_V_Legacy
- OS: Debian 13.8
- Desktop: Gnome 48 `wayland`
- Kernel: [7.3.6](https://github.com/azagramac/linux-kernel/releases/tag/kernel-135)


## Metal Gear Solid V
<img width="460" height="215" alt="image" src="https://github.com/user-attachments/assets/204a6ea3-bbd6-4e4a-a8f0-2fd7f87476c1" />

- Proton version: [GE-Proton10-27](https://github.com/GloriousEggroll/proton-ge-custom/releases/tag/GE-Proton10-27)
- Command:
  ```bash
  WINE_FULLSCREEN_FSR=1 WINE_FULLSCREEN_FSR_MODE=quality WINE_FULLSCREEN_FSR_CUSTOM_MODE=1920x1080 DXVK_ASYNC=1 PROTON_FSR3_UPGRADE=1 RADV_PERFTEST=aco PROTON_ENABLE_WAYLAND=1 gamemoderun %command%
  ```
- Settings game:

  <img width="800" alt="settings_mgsv" src="https://github.com/user-attachments/assets/1931797a-6f08-4374-ae0f-9f33d2a1b499" />

- Link: https://store.steampowered.com/app/287700/METAL_GEAR_SOLID_V_THE_PHANTOM_PAIN/
- OS: Debian 13
- Desktop: Gnome 48 `wayland`
- Kernel: [6.18.2](https://github.com/azagramac/linux-kernel/releases/tag/kernel-6.18.2)

---

## Metal Gear Solid 3
<img width="460" alt="image" src="https://github.com/user-attachments/assets/88dfb696-a475-405c-aef1-1cccad75f266" />

- Proton version: [GE-Proton10-27](https://github.com/GloriousEggroll/proton-ge-custom/releases/tag/GE-Proton10-27)
- Command:
  ```bash
  WINE_FULLSCREEN_FSR=1 WINE_FULLSCREEN_FSR_MODE=quality WINE_FULLSCREEN_FSR_CUSTOM_MODE=1920x1080 %command%
  ```
  ⚠️ When adding `RADV_PERFTEST=aco PROTON_ENABLE_WAYLAND=1`, the controller stops working in the game.

- Settings game:

  <img width="800" alt="settings_mgs3" src="https://github.com/user-attachments/assets/429ca661-4b28-4be6-b250-23bb5609cbf3" />

- Link: [MASTER COLLECTION Vol.1](https://store.steampowered.com/sub/886315/)
- Link: https://store.steampowered.com/app/2131650/METAL_GEAR_SOLID_3_Snake_Eater__Master_Collection_Version/
- OS: Debian 13
- Desktop: Gnome 48 `wayland`
- Kernel: [6.18.2](https://github.com/azagramac/linux-kernel/releases/tag/kernel-6.18.2)

---

## Mortal Kombat 11 Ultimate
<img width="460" height="215" alt="image" src="https://github.com/user-attachments/assets/2ce8cf5f-e5d7-46c9-87a5-ac33454f59a3" />

- Proton version: [GE-Proton10-27](https://github.com/GloriousEggroll/proton-ge-custom/releases/tag/GE-Proton10-27)
- Command:
  ```bash
  WINE_FULLSCREEN_FSR=1 WINE_FULLSCREEN_FSR_MODE=quality WINE_FULLSCREEN_FSR_CUSTOM_MODE=1920x1080 DXVK_ASYNC=1 PROTON_FSR3_UPGRADE=1 RADV_PERFTEST=aco PROTON_ENABLE_WAYLAND=1 gamemoderun %command%
  ```
- Settings game:

  <img width="800" alt="settings_mk" src="https://github.com/user-attachments/assets/889988b1-055d-459d-b05c-3c1dd32946d7" />

- Link: https://store.steampowered.com/app/976310/Mortal_Kombat_11/
- OS: Debian 13
- Desktop: Gnome 48 `wayland`
- Kernel: [6.18.2](https://github.com/azagramac/linux-kernel/releases/tag/kernel-6.18.2)

---

## Resident Evil 2
<img width="460" alt="image" src="https://github.com/user-attachments/assets/8806a787-0343-4574-b65d-740a77449cb2" />

- Proton version: [GE-Proton10-27](https://github.com/GloriousEggroll/proton-ge-custom/releases/tag/GE-Proton10-27)
- Command:
  ```bash
  WINE_FULLSCREEN_FSR=1 WINE_FULLSCREEN_FSR_MODE=quality WINE_FULLSCREEN_FSR_CUSTOM_MODE=1920x1080 DXVK_ASYNC=1 PROTON_FSR3_UPGRADE=1 RADV_PERFTEST=aco PROTON_ENABLE_WAYLAND=1 gamemoderun %command%
  ```
- Settings game:

  <img width="800" alt="settings_re2_1" src="https://github.com/user-attachments/assets/8a47cc5f-9c2a-47b1-85ea-c75314c485ce" />
  <img width="800" alt="settings_re2_2" src="https://github.com/user-attachments/assets/7559cf50-57b2-4161-a545-ed5472b5da10" />
  <img width="800" alt="settings_re2_3" src="https://github.com/user-attachments/assets/66ead638-fc3f-4fc1-911d-332cc5692198" />
  <img width="800" alt="settings_re2_4" src="https://github.com/user-attachments/assets/c1690aea-be41-4cc2-9f7f-fa138ac04b7b" />

- Link: https://store.steampowered.com/app/883710/Resident_Evil_2/
- OS: Debian 13
- Desktop: Gnome 48 `wayland`
- Kernel: [6.18.2](https://github.com/azagramac/linux-kernel/releases/tag/kernel-6.18.2)

---

## Resident Evil 3
<img width="460" alt="image" src="https://github.com/user-attachments/assets/4d4e5417-2446-49c6-9ebb-3d787360997c" />

- Proton version: [GE-Proton10-27](https://github.com/GloriousEggroll/proton-ge-custom/releases/tag/GE-Proton10-27)
- Command:
  ```bash
  WINE_FULLSCREEN_FSR=1 WINE_FULLSCREEN_FSR_MODE=quality WINE_FULLSCREEN_FSR_CUSTOM_MODE=1920x1080 DXVK_ASYNC=1 PROTON_FSR3_UPGRADE=1 RADV_PERFTEST=aco PROTON_ENABLE_WAYLAND=1 gamemoderun %command%
  ```
- Settings game:

  <img width="800" alt="settings_re3_1" src="https://github.com/user-attachments/assets/24feb964-1baf-42d8-b03d-5607ca0602f6" />
  <img width="800" alt="settings_re3_2" src="https://github.com/user-attachments/assets/dfb43a10-6343-477f-88e4-c70793971fd9" />
  <img width="800" alt="settings_re3_3" src="https://github.com/user-attachments/assets/a4153e4e-a5bc-411d-b06b-00dce3f20100" />
  <img width="800" alt="settings_re3_4" src="https://github.com/user-attachments/assets/492fc473-19ee-4d72-9452-2bea07370202" />

- Link: https://store.steampowered.com/app/952060/Resident_Evil_3/
- OS: Debian 13
- Desktop: Gnome 48 `wayland`
- Kernel: [6.18.2](https://github.com/azagramac/linux-kernel/releases/tag/kernel-6.18.2)

---

## Resident Evil 4
<img width="460" alt="image" src="https://github.com/user-attachments/assets/b9684cbc-66c5-4c4b-a8e5-605d36e9351d" />

- Proton version: [GE-Proton10-27](https://github.com/GloriousEggroll/proton-ge-custom/releases/tag/GE-Proton10-27)
- Command:
  ```bash
  WINE_FULLSCREEN_FSR=1 WINE_FULLSCREEN_FSR_MODE=quality WINE_FULLSCREEN_FSR_CUSTOM_MODE=1920x1080 DXVK_ASYNC=1 PROTON_FSR3_UPGRADE=1 RADV_PERFTEST=aco PROTON_ENABLE_WAYLAND=1 gamemoderun %command%
  ```
- Settings game:

  <img width="800" alt="settings_re4_1" src="https://github.com/user-attachments/assets/fd001073-efbc-42e3-8d70-483d01fd23e4" />
  <img width="800" alt="settings_re4_2" src="https://github.com/user-attachments/assets/86ec03f8-07d5-4f59-9c81-8704b842fe01" />
  <img width="800" alt="settings_re4_3" src="https://github.com/user-attachments/assets/2473c4ae-4a6c-4b1f-8167-5ee4cebac23e" />
  <img width="800" alt="settings_re4_4" src="https://github.com/user-attachments/assets/9eac92b0-1b6d-48d3-b554-d9c91df3682b" />

- Link: https://store.steampowered.com/app/2050650/Resident_Evil_4/
- OS: Debian 13
- Desktop: Gnome 48 `wayland`
- Kernel: [6.18.2](https://github.com/azagramac/linux-kernel/releases/tag/kernel-6.18.2)

---
