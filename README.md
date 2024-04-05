# Building reproducible NixOS images with systemd-repart

NixOS images can be built with [systemd-repart](https://www.freedesktop.org/software/systemd/man/latest/systemd-repart.html). This is especially well-suited for building immutable appliances, as it has built-in support for DM-verity, DM-crypt, and so forth. NixOS provides a convenient [utility module](https://github.com/NixOS/nixpkgs/blob/master/nixos/modules/image/repart.nix) for building such images from a NixOS configuration.

Unfortunately, the images built through systemd-repart are not entirely reproducible yet. In the case of invoking systemd-repart through the NixOS module, this has 3 main causes:

- systemd-repart itself being irreproducible in its behavior. (toy example: systemd-repart tagging files with an unfixable timestamp before copying them into the image)
- Filesystem-creation utilities being irreproducible in their behavior. To populate partitions with filesystems, systemd-repart calls `mkfs` utilities, which can (and have proven to) act irreproducible at times. (toy example: `mkfs.foo` using an unfixable random seed for a filesystem hash tree)
- NixOS invoking systemd-repart in a way or doing other modifications to the image-building so that the image built is irreproducible. (toy example: The NixOS systemd-repart module not using a timestamp-fixing option in systemd-repart)

This repository should serve as a place to collect such irreproducibility issues and to fix them together, as well as to keep track of which images can already be built reproducibly.

## The Status Quo

The reproducibility of NixOS systemd-repart images heavily depends on how the NixOS systemd-repart module is configured. Some filesystems can already be built reproducibly with the module, some cannot. Some are reproducible out-of-the-box with the builder, some are not or require non-default options to be set.

The repository lists proven-to-be-(ir)reproducible configurations in `reproducible` and `irreproducible`, respectively. You are welcome to submit minimal configuration examples for such images as a PR to this repository.

## Tracking Irreproducibility

Each irreproducible configuration should come with a dedicated markdown file, listing at least:

- What's (not) reproducible about the image. (e.g. Partition A is not reproducible, others are)

And, if available, also listing:

- Already-tracked-down reproducibility issues. (e.g. Unfixed timestamp in partition A's header)
- Which code is deemed responsible for the specific irreproducibility issue.
- How a fix could look like.
