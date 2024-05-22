# pbuild
[![build-ublue](https://github.com/askpng/pbuild/actions/workflows/build.yml/badge.svg)](https://github.com/askpng/pbuild/actions/workflows/build.yml)

> This image is set to automatically build every day at 18:00 UTC and upon `recipes/*.yml` updates.

Build intended as a learning project. Maybe one day this will turn into the perfect system for my computers to run on, who knows!

Once this image is deemed stable and polished enough, I plan to change the `pbuild` name or create a new repo altogether.

This build is a (hopefully) long-term learning project. My main goal is to better understand OCI conceptually and practically - another goal is to eventually end up with the ideal base image for my laptop and future devices.

Would not be possible without the power of [BlueBuild](https://blue-build.org/how-to/setup/)!

# Image details
- [BlueBuild Template](https://github.com/blue-build/template) with actions set up
- [silverblue-main](https://github.com/ublue-os/main/pkgs/container/silverblue-main) as base image. `40` only; will advance to 41 about a month after it releases.

## Default packages
In addition to the default packages installed in the `silverblue-main` base image, the following packages are installed by default. 
- `butter` by [zhangyuannie](https://github.com/zhangyuannie/butter)
- `blackbox-terminal`, eventually will be replaced with Ptyxis
- `epson-inkjet-printer-escpr` and `epson-inkjet-printer-escpr2`
- `fastfetch`
- `fish`
- `firewall-config`
- `gnome-shell-extension-gsconnect` to ensure all dependencies are installed
- `ibus-mozc` for experimental purposes (`ibus-anthy` has better toggles for non-JIS keyboards)
- `igt-gpu-tools`
- `pulseaudio-utils`
- `rsms-inter-fonts`, eventually will be replaced with fonts installed via the `fonts` module
- `thefuck` - a must have!
- `wl-clipboard`

The following packages are removed from the base image.
- `firefox` and `firefox-langpacks` - Firefox will be installed as a user Flatpak 
- `gnome-software-rpm-ostree`
- `gnome-tour`
- `gnome-terminal-nautilus`
- `gnome-terminal`
- `htop`
- `nvtop`
- `yelp`

### TBA - Packages
- `btop`
- `kitty`, my preferred terminal

## T480/s packages
The following packages are installed by default for improving Lenovo T480/s power management, performance, and features:
- `python-validity` forked by [sneexy](https://copr.fedorainfracloud.org/coprs/sneexy/python-validity/)
- `tlp` and `tlp-rdw`
- `throttled`
> `throttled` is shipped with [default values](https://github.com/erpalma/throttled/blob/master/etc/throttled.conf) but slightly different defaults. I use universal values for AC and battery so there is no `[UNDERVOLT.AC]` nor `[UNDERVOLT.BATTERY]`, only `[UNDERVOLT]`. Documented in the `throttled` [README](https://github.com/erpalma/throttled#undervolt).
- `zcfan` for easy and straightforward fan control
> `zcfan` needs `rpm-ostree kargs --append=thinkpad_acpi.fan_control=1` to work.

The following packages are explicitly removed from the base image due to conflicts.
- `fprintd`
- `fprintd-pam`
- `power-profiles-daemon`
- `thermald`

`tlp.service` is enabled by default. `systemd-rfkill.{service,socket}` is disabled by default. Eventually, I would like to enable `python-validity`, `throttled` and `zcfan` services by default as well if possible.

## Automatic updates
At the moment, automatic updates follow BlueBuild defaults. Eventually, I would like to set `rpm-ostreed-automatic.{timer,service}` to 17:30~18:30 UTC by default.

## Flatpak
The following apps are installed as *system* Flatpaks by default.
- BoxBuddy
- Clapper
- ExtensionManager
- FSearch
- File Roller
- Flatseal
- GNOME Clocks
- GNOME Document Viewer (Evince)
- GNOME Firmware
- GNOME Image Viewer (Loupe)
- GNOME Passwords and Keys (Seahorse)
- GNOME Text Editor
- Junction
- Mission Center
- Ptyxis
- TLP UI
- Warehouse

The following apps are installed as *user* Flatpaks by default.
- Chromium
- Firefox
- TextPieces
- Webcord

### TBA - System Flatpaks
- Celluloid
- Evolution
- ✅ File Roller
- GNOME Calendar
- ✅ GNOME Camera (Snapshot)
- ✅ GNOME Web (Epiphany) 
- ✅ GNOME Weather
- ✅ Kooha
- ✅ Main Menu (Libre Menu Editor)

### TBA - User Flatpaks
- Bitwarden
- Flameshot
- Fragments
- ✅ QuodLibet
- Spotify

> Alternatively, the apps above will be available for installation via `yafti`.

## Gnome Extensions
The following extensions are explicitly installed via `gnome-extensions` module. Eventually will be replaced with GSettings schemas. 
- Alphabetical App Grid
- AppIndicators Support
- Blur My Shell
- Dash-to-Dock
- Caffeine
- Just Perfection
- Light Style
- Logo Menu
- Night Theme Switcher
- QSTweak

# Installation

> Do at your own risk. This build is a heavy work in progress and ~~even I don't use it on bare metal~~ I do use it, but I guarantee nothing. Changes, especially to `recipes/`, will be frequent.

This build works alright but it's still loose in a lot of places.

If you managed to even get here and read this far, first of all, why? Second of all, maybe you shouldn't, but if you insist, do try out and help me make this a better build (please).

## Rebase
To rebase from a Silverblue installation, follow the steps below.
1. Rebase to the unsigned image to get the proper signing keys + policies installed and reboot:
  ```
  rpm-ostree rebase ostree-unverified-registry:ghcr.io/askpng/pbuild:latest --reboot
  ```
2. Rebase to the signed image and reboot:
  ```
  rpm-ostree rebase ostree-image-signed:docker://ghcr.io/askpng/pbuild:latest --reboot
  ```

## ISO
ISO file for a fresh install can be generated using `docker` or `podman` from a Silverblue system.

### Via Docker
```
mkdir ./iso-output
sudo docker run --rm --privileged --volume ./iso-output:/build-container-installer/build --pull=always \
ghcr.io/jasonn3/build-container-installer:latest \
IMAGE_REPO=ghcr.io/askpng \
IMAGE_NAME=pbuild \
IMAGE_TAG=latest \
VARIANT=Silverblue
```

### Via Podman
```
mkdir ./iso-output
sudo podman run --rm --privileged --volume ./iso-output:/build-container-installer/build --security-opt label=disable --pull=newer \
ghcr.io/jasonn3/build-container-installer:latest \
IMAGE_REPO=ghcr.io/askpng \
IMAGE_NAME=pbuild \
IMAGE_TAG=latest \
VARIANT=Silverblue
```

## Verification
These images are signed with [Sigstore](https://www.sigstore.dev/)'s [cosign](https://github.com/sigstore/cosign).

### Verify `cosign.pub`

```bash
cosign verify --key cosign.pub ghcr.io/askpng/pbuild
```
