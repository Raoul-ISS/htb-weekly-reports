# Week 05 – HTB: SEPC (Reversing, Medium)

**Student:** Raoul  
**Date:** 2025-11-11  
**Challenge:** https://app.hackthebox.com/challenges/SEPC  
**Category:** Reversing • **Difficulty:** Medium

## Summary

In SEPC I analyzed a small Linux kernel challenge packaged as a `bzImage` with a compressed `initramfs.cpio.gz`. Instead of running the provided QEMU script, I unpacked the initramfs, located a kernel module called `checker.ko`, and dumped its `.rodata` section. By XORing two adjacent blocks of data from `.rodata` in a short Python script, I recovered the flag `HTB{grabbing_d4t4_fr0m_k3rn3l5p4c3}` and submitted it successfully.

## Setup & Environment

- Kali Linux 2025.3 VM  
- ZIP downloaded from the HTB SEPC challenge page  
- Files extracted into `~/Downloads/rev_sepc`

cd ~/Downloads
unzip SEPC.zip              # password: hackthebox
cd rev_sepc
ls
file bzImage initramfs.cpio.gz run.sh
sed -n '1,200p' run.sh

Unpacking the Initramfs
mkdir -p /tmp/rev_sepc_init
cd /tmp/rev_sepc_init
gunzip -c ~/Downloads/rev_sepc/initramfs.cpio.gz | cpio -idmv

ls -l checker checker.ko
file checker checker.ko

Extracting .rodata From the Kernel Module
objcopy checker.ko --dump-section .rodata=rodata
ls -l rodata
hexdump -C rodata | head -n 16
The layout of .rodata suggested that two equal-length buffers were used to hide the flag with a simple XOR.

Recovering the Flag
cd /tmp/rev_sepc_init

python3 - << 'PY'
d = open("rodata", "rb").read()
s1 = d[0x20:0x60]
s2 = d[0x60:]
print("".join(chr(s1[i] ^ s2[i]) for i in range(len(s2))))
PY
# HTB{grabbing_d4t4_fr0m_k3rn3l5p4c3}
I then submitted the flag on Hack The Box and the SEPC challenge was marked as completed.

Final Flag
HTB{grabbing_d4t4_fr0m_k3rn3l5p4c3}
