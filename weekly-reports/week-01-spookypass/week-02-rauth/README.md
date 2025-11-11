Week 02 — HTB: RAuth (Reversing, Easy)
=====================================

**Student:** Raoul  
**Date:** 2025-11-10  
**Challenge:** https://app.hackthebox.com/challenges/RAuth  
**Category:** Reversing • **Difficulty:** Easy  

## Summary

In this challenge I reversed a Rust authentication binary that protects a hidden flag
behind a password prompt. By extracting the cryptographic material from the binary
and decrypting the ciphertext, I recovered the correct password and used it to get
the flag from the remote instance.

## Recon & Initial Analysis

I ran the `rauth` binary locally and saw a simple login prompt asking for a password.
Basic inputs showed that wrong passwords just return a generic error, so I used
`strings` and other inspection tools instead of brute force.

## Reversing and Key Extraction

Using `strings` and then `radare2` on the binary, I located suspicious long hex
strings in the `.rodata` section. These turned out to be the Salsa20 key, the nonce,
and the ciphertext that the program uses to validate the correct password.

## Decryption Script

I wrote a Python script with PyCryptodome’s `Salsa20` cipher, feeding it the extracted
key, nonce, and ciphertext. Decrypting the ciphertext produced the plaintext password
used by the login portal.

## Getting the Flag

First I tested the password locally by piping it into the `rauth` binary and
confirming that I was “Successfully Authenticated”. Then I connected to the remote
HTB instance with `nc`, sent the same password, and the service returned the final
flag.
