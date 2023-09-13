# Lab 1a: Patching a binary with Ghidra

## Overview

This guide will show you how to patch our target binary, `convert`, and fuzz it with Mayhem.

**Time to complete**: About 30 minutes


## Step 0. What's the problem?

When we run imagemagick's `convert` command against a valid png, it works!

```
$ ./convert corpus/nsa-insignia-sm.png /tmp/out.png
Success!
```

With an invalid png, we get this error:

```
$ ./convert corpus/nsa-insignia-crc-error.png /tmp/out.png
convert: IDAT: CRC error `corpus/nsa-insignia-crc-error.png' @ error/png.c/MagickPNGErrorHandler/1713.
convert: no images defined `/tmp/out.png' @ error/convert.c/ConvertImageCommand/3322.
Fail!
```

We need to patch the binary to make this work!

## Step 1. Find the error

The error is coming from a library that imagemagick depends on. How can we find it?
One was is to search for the string "CRC error" within each library. `ldd` comes in handy
here.

```
$ for file in $(ldd ./convert | sed -e 's/.*> \(.*\) (.*/\1/g'); do grep "CRC error" $file; done

grep: linux-vdso.so.1: No such file or directory
grep: (0x00007fff8434c000): No such file or directory
grep: /usr/lib/libpng16.so.16: binary file matches
```

Aha! Now we need to load this library into Ghidra and patch it.

# Step 2. Find the error (in the disassembly)
