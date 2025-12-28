## 🎮 Install gamemode
```bash
sudo apt install -y gamemode
```

## 🔍 Check service gamemode
```bash
systemctl --user status gamemoded
```
output:
```bash
○ gamemoded.service - gamemoded 
      Loaded: loaded (/usr/lib/systemd/user/gamemoded.service; 
      disabled; 
      preset: enabled) 
      Active: inactive (dead)
```

## 🧪 Run test
```bash
gamemoded -t
```
output:
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

## 🕹️ Test in game
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
