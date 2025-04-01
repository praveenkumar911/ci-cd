# 8. Dual Root with Data Volume

Date: 2018-02-17

## Status

Accepted

Extended by [9. Docker state directory on Data Volume](0009-docker-state-directory-on-data-volume.md)

Extended by [11. Preinstallable images on data volume](0011-preinstallable-images-on-data-volume.md)

## Context

Embedded systems require a mechanism to keep software up to date. We aim for **atomic updates** (updates that either fully succeed or fully fail, leaving the system in a consistent state) rather than a per-package approach to simplify support. Additionally:
- Users should have the option to reboot into a stable version of the system if an update fails due to client-side errors, miscommunication, or a malfunctioning release.
- Some configuration files must persist through updates.
- The root filesystem should remain immutable to allow checksumming and address security concerns.

To achieve this, we considered two approaches:
1. Point-mounting specific files from a read-write partition.
2. Setting up a read-write overlay that automatically holds all modifications without copying the underlying read-only system.

## Decision

We will set up **two root partitions**: one active and one dormant. Updates will rewrite the dormant partition, and the system will switch to it on the next reboot. Key implementation details include:
- Root partitions will be locked as read-only.
- Configuration files will be bind-mounted from a read-write partition.
- A read-write "data" volume/partition will be set up and expanded to the end of the disk for modifiable configurations and other data files.

For example, modifiable configuration files might include `/etc/network/interfaces` or `/etc/hostname`.

## Consequences

- `+` The system always has a stable version of the OS to fall back on.
- `+` The root filesystem can be checksummed and locked as read-only, enhancing security and integrity.
- `+` System configuration is separate and can easily be backed up or ported to other instances.
- `+` We can explicitly designate which configuration files can be modified.
- `-` Dual root requires twice as much disk space for system files.
- `-` Atomic updates result in larger update files compared to a per-package approach.
- `-` Each modifiable configuration file must be identified and managed manually.

## Future Considerations

Advancements in storage technology or alternative update mechanisms (e.g., delta updates) may reduce the need for dual root partitions in the future. Additionally, automating the identification of modifiable configuration files could streamline the process.
