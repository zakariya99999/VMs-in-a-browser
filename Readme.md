Building
-

See `docker/test-image/Dockerfile` for a full setup on Debian.

You need:

- java
- gcc, make, libc-i386
- nasm, gdb and qemu (for running tests)
- rust-nightly with the wasm32-unknown-unknown target
- nodejs (a latest version is required, 10.11.0 is known to be working)

Run `make all-debug` to build the debug build (at `debug.html`).
Run `make all` to build the optimized build (at `index.html`).

Testing
-

Run all tests: `make jshint rustfmt kvm-unit-test nasmtests nasmtests-force-jit expect-tests jitpagingtests qemutests rust-test tests`

Creating a Linux image for use in v86
-

See `docker/debian-full/Readme.md`.


Below is the readme of the open-source version of v86 (not everything applies)
=

API examples
-

- [Basic](examples/basic.html)
- [Programatically using the serial terminal](examples/serial.html)
- [A Lua interpreter](examples/lua.html)
- [Two instances in one window](examples/two_instances.html)
- [Saving and restoring emulator state](examples/save_restore.html)

Using v86 for your own purposes is as easy as:

```javascript
var emulator = new V86Starter({
    screen_container: document.getElementById("screen_container"),
    bios: {
        url: "../../bios/seabios.bin",
    },
    vga_bios: {
        url: "../../bios/vgabios.bin",
    },
    cdrom: {
        url: "../../images/linux.iso",
    },
    autostart: true,
});
```

See [API](docs/api.md).


How does it work?
-

v86 emulates an x86-compatible CPU and hardware. Here's a list of emulated hardware:

- An x86 compatible CPU. The instruction set is around Pentium 1 level. Some
  features are missing, more specifically:
  - Task gates, far calls in protected mode
  - 16 bit protected mode features
  - Single stepping
  - MMX, SSE
  - A bunch of FPU instructions
  - Some exceptions
- A floating point unit (FPU). Calculations are done with JavaScript's double
  precision numbers (64 bit), so they are not as precise as calculations on a
  real FPU (80 bit).
- A floppy disk controller (8272A).
- An 8042 Keyboard Controller, PS2. With mouse support.
- An 8254 Programmable Interval Timer (PIT).
- An 8259 Programmable Interrupt Controller (PIC).
- A CMOS Real Time Clock (RTC).
- A VGA controller with SVGA support and Bochs VBE Extensions.
- A PCI bus. This one is partly incomplete and not used by every device.
- An IDE disk controller.
- An NE2000 (8390) PCI network card.
- A virtio filesystem.


Testing
-

The disk images are not included in this repository. You can download them
directly from the website using:

`wget -P images/ https://copy.sh/v86/images/{linux.iso,linux3.iso,kolibri.img,windows101.img,os8.dsk,freedos722.img,openbsd.img}`.

A testsuite is available in `tests/full/`. Run it using `node tests/full/run.js`.


How to build, run and embed?
-

- Building is only necessary for releases, open debug.html and everything should load out of the box
- If you want a compressed and fast (i.e. with debug code removed) version, you
  need Closure Compiler. Download it as shown below and run `make build/v86_all.js`.
- ROM and disk images are loaded via XHR, so if you want to try out `index.html`
  locally, make sure to serve it from a local webserver. You can use `make run`
  to serve the files using Python's SimpleHTTPServer.
- If you only want to embed v86 in a webpage you can use libv86.js. For
  usage, check out the [API](docs/api.md) and [examples](examples/).
- A couple of disk images are provided for testing. You can check them out
  using `wget -P images/ https://copy.sh/v86/images/{linux.iso,linux3.iso,kolibri.img,windows101.img,os8.dsk,freedos722.img,openbsd.img}`.


**Short summary:**

```bash
# grab the main repo
git clone https://github.com/copy/v86.git && cd v86

# grab the disk images
wget -P images/ https://copy.sh/v86/images/{linux.iso,linux3.iso,kolibri.img,windows101.img,os8.dsk,freedos722.img,openbsd.img}

# grab closure compiler
wget -P closure-compiler https://dl.google.com/closure-compiler/compiler-latest.zip
unzip -d closure-compiler closure-compiler/compiler-latest.zip *.jar

# build the library
make build/libv86.js

# run the tests
./tests/full/run.js
```

Compatibility
-

Here's an overview of the operating systems supported in v86:

- Linux works pretty well. Graphical boot fails in many versions, but you
  mostly get a shell. The mouse is often not detected automatically.
  - Damn Small Linux (2.4 Kernel): Works, takes circa 10 minutes to boot.
  - Tinycore (3.0 kernel): `udev` and `X` fail, but you get a
    terminal.
  - Nanolinux works.
  - Archlinux works with some caveats. See [archlinux.md](docs/archlinux.md).
- ReactOS works
- FreeDOS, Windows 1.01 and MS-DOS run very well.
- KolibriOS works. A few applications need SSE.
- Haiku boots, but takes very long (around 30 minutes).
- No Android version seems to work, you still get a shell.
- Windows 1, 95 and 98 work. Other versions currently don't.
- Many hobby operating systems work.
- FreeBSD works

You can get some infos on the disk images here: https://github.com/copy/images.


How can I contribute?
-

- Add new features (hardware devices, fill holes in the CPU), fix bugs. Check
  out the issues section and contact me if you need help.
- Report bugs.
- If you want to donate, let me know.

License
-

Simplified BSD License, see [LICENSE](LICENSE), unless otherwise noted.


Credits
-

- CPU test cases via QEMU, http://wiki.qemu.org/Main_Page
- More tests via [kvm-unit-tests](https://www.linux-kvm.org/page/KVM-unit-tests)
- [Disk Images](https://github.com/copy/images)
- [The jor1k project](https://github.com/s-macke/jor1k) for 9p, filesystem and uart drivers
- [WinWorld](https://winworldpc.com/) sources of some old operating systems


More questions?
-

Shoot me an email to `copy@copy.sh`. Please don't tell about bugs via mail,
create a bug report on GitHub instead.


Author
-

Fabian Hemmer (http://copy.sh/, `copy@copy.sh`)

