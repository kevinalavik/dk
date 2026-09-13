# Limine DC Template

This repository will demonstrate how to set up a basic x86-64 kernel in DC using Limine.

DC is a small freestanding-first systems language; see [Personne-admin/dcc](https://github.com/Personne-admin/dcc) for the compiler, language spec, and installation instructions.

It is recommended to cross reference the contents of this repository with [the Limine Bare Bones](https://osdev.wiki/wiki/Limine_Bare_Bones) OSDev wiki page.

## How to use this?

### Dependencies

Any `make` command depends on GNU make (`gmake`) and is expected to be run using it. This usually means using `make` on most GNU/Linux distros, or `gmake` on other non-GNU systems.

All `make all*` targets depend on the `dcc` DC compiler (see the `DCC` `make` variable) capable of targeting `x86_64-elf`, and a GNU-compatible linker (`ld` by default, see the `LD` `make` variable) capable of linking x86-64 ELF objects.

Building also requires `curl`, used to download the Limine release and the EDK2 OVMF firmware images, and a C compiler for the host (`cc` by default, see the `HOST_CC` `make` variable), used to build the `limine` host utility.

Additionally, building an ISO with `make all` requires `xorriso`, and building a HDD/USB image with `make all-hdd` requires `sgdisk` (usually from `gdisk` or `gptfdisk` packages) and `mtools`. The `run` targets require `qemu`.

### Makefile targets

Running `make all` will compile the kernel (from the `kernel/` directory) and then generate a bootable ISO image.

Running `make all-hdd` will compile the kernel and then generate a raw image suitable to be flashed onto a USB stick or hard drive/SSD.

Running `make run` will build the kernel and a bootable ISO (equivalent to make all) and then run it using `qemu` (if installed).

Running `make run-hdd` will build the kernel and a raw HDD image (equivalent to make all-hdd) and then run it using `qemu` (if installed).

The `run-uefi` and `run-hdd-uefi` targets are equivalent to their non `-uefi` counterparts except that they boot `qemu` using a UEFI-compatible firmware.
