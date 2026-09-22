# HARDCORE LINUX, a lightweight independent Linux rootfs and distro

### installation: see wiki, or download the .img directly if you're impatient

### goals
Most Linux distros nowadays can start at 2+ GB ISO, the most notable example being Ubuntu with a 6GB ISO for the full GUI variant
or Omarchy (which does not even try to be minimal).

Some distros try, like Arch delivers a 700MB base rootfs; and some even harder, like Alpine, and some tried too hard, like
TinyCore (impressive for 17MB, but almost nothing works).

Hardcore tries to be minimal while not giving away functionality.

Hardcore, unlike many distros, uses musl libc instead of glibc, which is lighter, safer and strictly POSIX.

The init system is a shell script that launches scripts and services from /system. A shell script is more auditable than a binary.

### package management

Hardcore Linux now has two package paths with a deliberate bootstrap boundary:

- `build/` keeps the legacy `flashpack` format for the base rootfs. This is necessary for bootstrap: the base system must be able to build and install the first `yspm` package before `yspm` itself exists.
- `build.native/` uses `yspm build` and produces native `.yspkg` packages. These packages carry structured metadata, dependencies, and lifecycle hooks.
- `tools/flashman` is the compatibility interface. When `yspm` is installed it delegates to `yspm`; otherwise it retains the legacy archive path for bootstrap packages.

The legacy `flashpack` builder also creates a no-op `install` hook automatically when a base recipe does not provide one, so bootstrap packages do not fail during `flashman` installation.

### desktop environment

Hardcore can run Sway on top of Wayland.

Installing the native `udev` package automatically installs a post-install hook that creates
`/system/scripts/00003-udevd`. At boot that script starts `udevd`, triggers subsystem and device events, and waits with `udevadm settle`.

The Sway package declares `udev` as a dependency and installs a post-install wrapper around `/usr/bin/sway`.
The wrapper sets `WLR_RENDERER=pixman` and then executes the real `sway.bin`, so the renderer no longer needs to be configured manually.

No manual udev startup script or renderer edit is required after installing the native Sway stack.

### development repository layout

- `build/` — bootstrap recipes and the legacy base-package builder
- `build.native/` — native package recipes built with `yspm`
- `tools/` — system tools such as init, initctl, and the flashman compatibility layer
- `repo/` — built package archives and repository metadata

### report bugs

If you ever find a bug or request a feature, start an issue at https://github.com/amazing2545/HardcoreLinux/issues
