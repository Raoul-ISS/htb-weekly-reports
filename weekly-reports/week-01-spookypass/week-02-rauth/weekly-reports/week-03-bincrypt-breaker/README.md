Week 03 — HTB: BinCrypt Breaker (Reversing, Medium)
===================================================

**Student:** Raoul  
**Date:** 2025-11-11  
**Challenge:** https://app.hackthebox.com/challenges/BinCrypt-Breaker  
**Category:** Reversing  •  **Difficulty:** Medium

## Summary

In this challenge I reversed a password-checking binary that decrypts an encrypted file and validates a flag. By extracting the XOR-based decryption routine, converting the encrypted blob into a valid ELF, and re-implementing the string transforms in Python, I recovered the correct flag and successfully validated it with the checker.

## Recon & Initial Analysis

I downloaded `BinCrypt Breaker.zip` from the HTB challenge page and extracted it with the password `hackthebox`, which produced a folder `rev_bincrypt_breaker` containing `checker` and `file.bin`. `file` and `checksec` showed that `checker` is a 64-bit PIE executable with standard protections enabled, while `file.bin` is opaque data. Running `./checker file.bin` prompted for a flag and printed “Wrong flag” for any random input, so I moved to static analysis of the binary instead of brute force.

## Decrypting file.bin

Using `objdump -d checker | grep -A 50 "decrypt"` I found a function that reads `file.bin`, XORs each byte with `0xAB`, and writes the result to memory. I reproduced this logic in a small Python script `decrypt_file.py` that reads `file.bin`, XORs every byte with `0xAB`, and writes the output to `decrypted_elf`. After running the script and marking the new file as executable, `file decrypted_elf` confirmed that it is a valid 64-bit ELF binary.

## Recovering the Flag

Dumping the `.rodata` section of `decrypted_elf` with `objdump -s -j .rodata decrypted_elf` revealed an encrypted flag-like string `RV{r15]_vcP3o]L_tazmfSTaa3s0` along with the prompt “Enter the flag (without `HTB{}`)”. By inspecting the surrounding code I reconstructed two helper functions: `transform1`, which XORs selected indices and permutes characters, and `transform2`, which swaps several positions in the combined string. I implemented these in `solve.py`, split the encrypted string in half, applied `transform1` with different XOR bytes (`0x02` and `0x03`), then `transform2`, and finally joined the characters to obtain the flag `HTB{cRyPto_r3V_15_always_aWeS0m3}`.

## Verification

To verify the solution I passed the inner flag string to the decrypted checker by running:

echo "cRyPto_r3V_15_aLways_aWeS0m3" | ./decrypted_elf
