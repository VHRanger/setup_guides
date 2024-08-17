

## Native Termux x11 desktop

https://github.com/LinuxDroidMaster/Termux-Desktops/blob/main/Documentation/native/termux_native.md


## Running Proot x11 desktop

Ivon Huang has a bunch of blogs on it: https://ivonblog.com/en-us/

### TRAPS:

Running a X11 desktop only works on alpine for some android 13 devices and newer. Linux kernel to proot lib mismatch causes glibc issue "child process could not launch". Since alpine uses musl instead it still works.