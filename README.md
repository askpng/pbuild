# pbuild &nbsp; [![build-ublue](https://github.com/askpng/pbuild/actions/workflows/build.yml/badge.svg)](https://github.com/askpng/pbuild/actions/workflows/build.yml)

I created this because I want to learn how this works and at the end of the day, I want a computer that runs itself the way I want it to, without unnecessary extras. Who knows what will come out of this. Probably nothing!

[BlueBuild docs](https://blue-build.org/how-to/setup/) for reference.

## Installation (Experimental!)

Rebase guide:

- Rebase to the unsigned image to get the proper signing keys + policies installed and reboot:
  ```
  rpm-ostree rebase ostree-unverified-registry:ghcr.io/askpng/pbuild:latest --reboot
  ```
- Rebase to the signed image and reboot:
  ```
  rpm-ostree rebase ostree-image-signed:docker://ghcr.io/askpng/pbuild:latest --reboot
  ```
## ISO

ISO file for a fresh install can be generated using `docker` or `podman` from a Silverblue system.

### Docker
```
mkdir ./iso-output
sudo docker run --rm --privileged --volume ./iso-output:/build-container-installer/build --pull=always \
ghcr.io/jasonn3/build-container-installer:latest \
IMAGE_REPO=ghcr.io/askpng \
IMAGE_NAME=pbuild \
IMAGE_TAG=latest \
VARIANT=Silverblue # should match the variant your image is based on
```
### Podman
```
mkdir ./iso-output
sudo podman run --rm --privileged --volume ./iso-output:/build-container-installer/build --security-opt label=disable --pull=newer \
ghcr.io/jasonn3/build-container-installer:latest \
IMAGE_REPO=ghcr.io/askpng \
IMAGE_NAME=pbuild \
IMAGE_TAG=latest \
VARIANT=Silverblue # should match the variant your image is based on
```

## Verification

These images are signed with [Sigstore](https://www.sigstore.dev/)'s [cosign](https://github.com/sigstore/cosign).

### Verify `cosign.pub`

```bash
cosign verify --key cosign.pub ghcr.io/askpng/pbuild
```
