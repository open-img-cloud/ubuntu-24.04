# Ubuntu 24.04 LTS cloud images

Cloud-init-ready, signed Ubuntu 24.04 LTS (Noble Numbat) qcow2 images for OpenStack and Proxmox,
part of the [openimages.cloud](https://openimages.cloud) catalog.

- Catalog page: <https://openimages.cloud/images/ubuntu-24.04/>
- Registry: <https://images.openimages.cloud/ubuntu-24.04/latest/>

## About

Ubuntu 24.04 LTS (Noble Numbat) is the long-term support release of Ubuntu published by Canonical ([https://ubuntu.com/](https://ubuntu.com/)). This repo takes the official upstream
server cloud image ([listing](https://cloud-images.ubuntu.com/releases/noble/)), verifies it against the
upstream `SHA256SUMS`, adds `qemu-guest-agent`, applies the openimages.cloud cloud-init
policy, boot-tests it, signs it with cosign (keyless) and publishes it.

The build pipeline is shared with the rest of
[`open-img-cloud`](https://github.com/open-img-cloud): this repo only ships
`VERSION`, `build/customize.sh`, `build/detect-upstream.sh` and two thin
caller workflows that delegate to the reusable workflows in
[`open-img-cloud/.github`](https://github.com/open-img-cloud/.github).

## Lifecycle

Ubuntu 24.04 LTS has standard support until **May 2029** (then Ubuntu Pro / ESM).

## Image

- File: `ubuntu-24.04-<version>-x86_64.qcow2`, where `<version>` is
  `<release>-<upstream serial>` (e.g. `24.04-20261017`).
- Default user: `ubuntu` (SSH key from cloud-init, passwordless sudo).
- The root filesystem grows to the flavor disk on first boot.

## Use it

```sh
V=24.04-20261017
curl -fLO https://images.openimages.cloud/ubuntu-24.04/$V/ubuntu-24.04-$V-x86_64.qcow2

openstack image create \
    --disk-format qcow2 --container-format bare \
    --property hw_qemu_guest_agent=yes \
    --file ubuntu-24.04-$V-x86_64.qcow2 \
    'Ubuntu 24.04 LTS '$V
```

Verification (cosign), Proxmox commands and provenance details are on the
[catalog page](https://openimages.cloud/images/ubuntu-24.04/) and in the
[verification guide](https://openimages.cloud/verify/).

## Release flow

1. `watch.yml` runs daily (06:50 UTC) and opens an
   `auto/upstream-bump` pull request when upstream publishes a new image.
2. Merge it, then push the tag `v<VERSION>`: `release.yml` builds, boot-tests,
   signs and publishes the image.

## License

The build tooling in this repository is [GPL-2.0](LICENSE). The image keeps
the licensing terms of Ubuntu (see https://ubuntu.com/legal/intellectual-property-policy; the image is redistributed unmodified apart from the documented customisation). Ubuntu is a trademark of Canonical; this project is not affiliated with it.
