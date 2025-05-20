

# Setup Guide: s390x Platform Pretest

This guide will help you replicate the setup of an emulated s390x Linux environment on an x86_64 Ubuntu machine. You'll compile a simple C program using a cross-compiler and run it in a chrooted Debian environment using QEMU.



## 🧰 Prerequisites

Make sure you're running Ubuntu (20.04 or newer) on an x86_64 system.



## Step 1: Clone the Repository

```bash
git clone https://github.com/your-username/s390x-pretest.git
cd s390x-pretest
````



## Step 2: Install Required Packages

```bash
sudo apt update && sudo apt install -y \
    qemu-user-static \
    binfmt-support \
    debootstrap \
    gcc-s390x-linux-gnu \
    libc6-dev-s390x-cross
```

These packages allow:

* Emulating s390x binaries (`qemu-s390x-static`)
* Bootstrapping a minimal Debian filesystem for s390x (`debootstrap`)
* Cross-compiling for s390x (`gcc-s390x-linux-gnu`)

---

## Step 3: Create a Root Filesystem for s390x

```bash
mkdir -p ~/s390x-vm/rootfs
sudo debootstrap --arch=s390x --foreign stable ~/s390x-vm/rootfs http://deb.debian.org/debian
```

---

## Step 4: Copy QEMU Static Binary

```bash
sudo cp /usr/bin/qemu-s390x-static ~/s390x-vm/rootfs/usr/bin/
```

> If you get a "Text file busy" error, close any terminals or applications using QEMU and try again.

---

## Step 5: Complete the Second Stage of Bootstrap

```bash
sudo chroot ~/s390x-vm/rootfs /debootstrap/debootstrap --second-stage
```

This finalizes the base Debian system setup inside the rootfs.

---

## Step 6: Compile the C Program for s390x

You can use the provided `test.c` file in the repo:

```bash
s390x-linux-gnu-gcc test.c -o test-s390x
```

Confirm the binary:

```bash
file test-s390x
# Output: ELF 64-bit MSB pie executable, IBM S/390, version 1 (SYSV), dynamically linked, interpreter /lib/ld64.so.1, BuildID[sha1]=a1bff220f4e32e4954e99da773a08db756577f74, for GNU
/Linux 3.2.0, not stripped  
```

---

## Step 7: Copy the Binary into the VM Rootfs

```bash
sudo cp test-s390x ~/s390x-vm/rootfs/root/
```

---

## Step 8: Enter the s390x Chroot Environment

```bash
sudo chroot ~/s390x-vm/rootfs /bin/bash
```

---

## ▶Step 9: Run the Test Binary

Inside the chroot:

```bash
cd /root
./test-s390x
```

You should see:

```
Hello from s390x!
```

---

## Done!

WE have Succesfully:
* Set up an s390x Debian rootfs on x86\_64
* Compiled a binary using a cross-compiler
* Verified execution using QEMU

---

## Screenshots

Refer to the `screenshots/` folder for output proofs:

* `compile-output.png`
* `chroot-output.png`
* `test-output.png`

---

## Cleanup

To delete the environment:

```bash
sudo rm -rf ~/s390x-vm
```

---
