WeensyOS
========

This is WeensyOS, a teaching operating system originally built for
Harvard’s [CS 61](https://cs61.seas.harvard.edu/) and lightly
adapted for Brown's [CS 131](https://cs131.cs.brown.edu/) and URI's [CSC 412](https://uri-csc412.github.io/web/).

Quickstart: `make run` will run the OS using the [QEMU](https://qemu.org/)
machine emulator.

Make targets
------------

Use `make run` to run the OS in a separate QEMU window. Close the QEMU
window, or type `q` inside it, to exit the OS.

Use `make run-console` to run the OS in the console window.

WeensyOS creates a debug log in `log.txt`. Run `make LOG=stdio run` to
redirect the debug log to the standard output, or `make
LOG=file:FILENAME run` to redirect it to `FILENAME`.

Run `make D=1 run` to ask QEMU to print verbose information about interrupts
and CPU resets to the standard error. This setting will also cause QEMU to
quit after encountering a [triple fault](https://en.wikipedia.org/wiki/
Triple_fault) (normally it will reboot).
Finally, run `make clean` to clean up your directory.

Building
--------

If you want to natively build WeensyOS outside of your course dev container.

**Linux:** WeensyOS should build natively on a Linux machine or
virtual machine. `qemu` packages are required to run WeensyOS; on
Ubuntu, `sudo apt install qemu qemu-system-x86` should work. A recent
compiler is required, GCC 7 or GCC 8 if possible.

**Mac OS X:** WeensyOS can build on Mac OS X after some tools are installed.

1. Install [Homebrew](https://brew.sh/).
2. Install Homebrew’s new GCC package: `brew install gcc`
3. Install Homebrew’s QEMU: `brew install qemu`
4. Tap [Sergio Benitez’s collection of cross-compilers](https://github.com/SergioBenitez/homebrew-osxct): `brew tap SergioBenitez/osxct`
5. Install the `x86_64-unknown-linux-gnu` cross-compiler toolchain: `brew install x86_64-unknown-linux-gnu`
6. Rename the file `config.mk.mac` in the main directory for thislab/assignment to build WeensyOS directory on Mac M-series.

```bash
cp config.mk.mac config.mk
```

    (Do not add `config.mk` to your repository, since it is intended for
    local configuration.)

Troubleshooting
---------------

If you experience runtime errors involving `obj/libqemu-nograb.so.1`, put
`QEMU_PRELOAD_LIBRARY=` in `config.mk`. This disables a shim we use that
prevents QEMU from grabbing the mouse.

If you’re having trouble killing a QEMU, forcibly close it by running
`make kill`.

Source files
------------

Real operating systems are big. We have tried to boil down this OS to
a minimum, comment it to help you, and separate x86-64 specifics from
more fundamental issues.

### Common files

| File            | Description                            |
| --------------- | -------------------------------------- |
| `types.h`       | Type definitions                       |
| `lib.hh/cc`     | Chickadee C library                    |
| `x86-64.h`      | x86-64 hardware definitions            |
| `elf.h`         | ELF64 structures for loading programs  |

### Boot loader

| File            | Description                  |
| --------------- | ---------------------------- |
| `bootentry.S`   | Boot loader entry point      |
| `boot.cc`       | Boot loader main code        |
| `link/boot.ld`  | Boot loader linker script    |

### Kernel core

| File                | Description                          |
| ------------------- | ------------------------------------ |
| `kernel.hh`         | Kernel declarations                  |
| `k-exception.S`     | Kernel entry points                  |
| `k-hardware.cc`     | Kernel initialization and hardware   |
| `k-vmiter.hh/cc`    | Page table iterators                 |
| `kernel.cc`         | Kernel exception handlers            |
| `k-memviewer.cc`    | Kernel memory viewer                 |
| `link/kernel.ld`    | Kernel linker script                 |

### Kernel libraries

| File                | Description                          |
| ------------------- | ------------------------------------ |
| `k-apic.hh`         | Interrupt controller hardware        |
| `k-pci.hh`          | PCI bus hardware                     |

### Processes

| File              | Description                                      |
| ----------------- | ------------------------------------------------ |
| `u-lib.cc/hh`     | Process library and system call implementations  |
| `p-*.cc`          | Process code                                     |
| `link/process.ld` | Process binary linker script                     |

Build files
-----------

The main output of the build process is a disk image,
`weensyos.img`. QEMU “boots” off this disk image, but the image
could conceivably boot on real hardware! The build process also
produces other files that can be useful to examine.

| File                         | Description                          |
| ---------------------------- | ------------------------------------ |
| `obj/kernel.asm`             | Kernel assembly (with addresses)     |
| `obj/kernel.sym`             | Kernel defined symbols               |
| `obj/p-allocator.asm`, `sym` | Same for process binaries            |
