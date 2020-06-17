---
layout: post
title: Testing libcamera on Virtme
date: 2020-06-17 16:30:00 +0530
image: libcamera1.png
tags: [libcamera]
categories: [Outreachy-Linux]
description: This write-up highlights how one can use Virtme, which is a QEMU wrapper used to run a virtualized Linux kernel, to test the changes made in libcamera on the latest kernel. Virtme is..
---
### What this blog is about

This write-up highlights how one can use [Virtme](https://github.com/amluto/virtme), which is a QEMU wrapper used to run a virtualized Linux kernel, to test the changes made in [libcamera](https://libcamera.org/index.html) on the latest kernel.

Virtme is good in the sense that it is easy to use, and makes testing kernel changes quite simple, without having to reboot the entire system again and again. I will walk through how we can use qcam, which is a testing application of libcamera, inside virtme.

### The steps

- Follow <https://libcamera.org/getting-started.html> to clone and compile libcamera on your native machine.
- Clone latest media_tree using:
    ```
    git clone git://linuxtv.org/media_tree.git
    ```
- Install virtme:
    ```
    pip3 install --user git+https://github.com/ezequielgarcia/virtme.git
    ```
- Compile Kernel with Virtme:
  - ```
    cd media_tree
    virtme-configkernel --defconfig
    ```
  - Enable vimc/vivid/ etc as per requirement
  - Enable CONFIG_DRM_VIRTIO_GPU=y in kernel config
  - make -j8

- Run virtme (on native machine)
  - Use a variable to pass memory and CPUs to be used by QEMU to prevent Out of Memory error. Adjust the values accordingly.
    ```
    export MEM_CPU="-m 2G -smp 2"
    ```
  - Run virtme
    ```
    virtme-run --graphics --kdir $KDIR --kopt="console=ttyS0" --qemu-opts $MEM_CPU -vga virtio -serial mon:stdio
    ```
    where $KDIR is  kernel directory's path
- To run qcam (in virtme):
    ```
    QT_QPA_PLATFORM=linuxfb /usr/local/bin/qcam 
    ```
