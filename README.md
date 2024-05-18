# pbuild &nbsp; [![build-ublue](https://github.com/askpng/pbuild/actions/workflows/build.yml/badge.svg)](https://github.com/askpng/pbuild/actions/workflows/build.yml)

Build intended as a learning project. Maybe one day this will turn into the perfect system for my computers to run on, who knows!

[BlueBuild docs](https://blue-build.org/how-to/setup/) for reference.

# Details
## Default packages
This image installs the following details by default:
- `epson-inkjet-printer-escpr` and `epson-inkjet-printer-escpr2` for Epson printers
- `fastfetch` because I like having it on my host system
- `fish` as it's what I prefer
- `firewall-config` for point-and-click firewall management
- `ibus-mozc` for testing purposes - I normally use `ibus-anthy` for its convenient IME & kana toggle
- `igt-gpu-tools` to monitor GPU use
- `pulseaudio-utils`
- `rsms-inter-fonts` because it's lovely
- `thefuck` so I can give a fuck
- `wl-clipboard`

Removed packages:
- `firefox` and `firefox-langpacks` - I prefer the Flatpak version
- `gnome-software-rpm-ostree`
- `gnome-tour`
- `yelp`
## BTRFS-related packages
BTRFS snapshots are handled by either `butter` or `snapper` and `btrfs-assistant`.
## T480/s packages
The following packages are installed by default for improving Lenovo T480/s power management, performance, and features:
- sneexy's fork of [python-validity](https://copr.fedorainfracloud.org/coprs/sneexy/python-validity/)
- `tlp` and `tlp-rdw`
- `throttled` for undervolting
- `zcfan` for fan control

> `zcfan` needs `rpm-ostree kargs --append=thinkpad_acpi.fan_control=1` to work.

The following packages are removed from the base due to conflicts:
- `fprintd`
- `fprintd-pam`
- `power-profiles-daemon`
- `thermald`

# Installation

> Do at your own risk. This build is a heavy work in progress and even I don't use it on bare metal.

Rebase guide:

- Rebase to the unsigned image to get the proper signing keys + policies installed and reboot:
  ```
  rpm-ostree rebase ostree-unverified-registry:ghcr.io/askpng/pbuild:latest --reboot
  ```
- Rebase to the signed image and reboot:
  ```
  rpm-ostree rebase ostree-image-signed:docker://ghcr.io/askpng/pbuild:latest --reboot
  ```
# ISO

ISO file for a fresh install can be generated using `docker` or `podman` from a Silverblue system.

## Docker
```
mkdir ./iso-output
sudo docker run --rm --privileged --volume ./iso-output:/build-container-installer/build --pull=always \
ghcr.io/jasonn3/build-container-installer:latest \
IMAGE_REPO=ghcr.io/askpng \
IMAGE_NAME=pbuild \
IMAGE_TAG=latest \
VARIANT=Silverblue # should match the variant your image is based on
```
## Podman
```
mkdir ./iso-output
sudo podman run --rm --privileged --volume ./iso-output:/build-container-installer/build --security-opt label=disable --pull=newer \
ghcr.io/jasonn3/build-container-installer:latest \
IMAGE_REPO=ghcr.io/askpng \
IMAGE_NAME=pbuild \
IMAGE_TAG=latest \
VARIANT=Silverblue # should match the variant your image is based on
```

# Verification

These images are signed with [Sigstore](https://www.sigstore.dev/)'s [cosign](https://github.com/sigstore/cosign).

## Verify `cosign.pub`

```bash
cosign verify --key cosign.pub ghcr.io/askpng/pbuild
```
