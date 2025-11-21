Week 06 – HTB: Cyberpsychosis (Reversing, Easy)
==============================================

Student: Raoul  
Date: 2025-11-21  
Challenge: https://app.hackthebox.com/challenges/Cyberpsychosis  
Category: Reversing • Difficulty: Easy  

Summary
-------

In Cyberpsychosis I reversed a malicious Linux kernel module `diamorphine.ko` that was deployed on a remote HTB instance. By performing static analysis with `file`, `rabin2`, `strings`, and then digging into the code with `radare2`, I identified special signal handlers inside functions such as `hacked_kill` that were used as a hidden backdoor. These signals allowed me to escalate from an unprivileged shell to root on the target, unload the rootkit, and then locate the flag under `/opt/psychosis/flag.txt`, completing the challenge.

Setup & Environment
-------------------

- Kali Linux 2025.3 virtual machine  
- Challenge ZIP downloaded from the Cyberpsychosis page on Hack The Box  
- Files extracted into `~/Downloads/rev_cyberpsychosis`  
- Remote challenge instance accessed with `ncat` from the Kali VM  

Initial File Analysis
---------------------

cd ~/Downloads
unzip Cyberpsychosis.zip        # password: hackthebox
cd rev_cyberpsychosis
ls
file diamorphine.ko
rabin2 -I diamorphine.ko
strings -n 4 diamorphine.ko | head -n 40
These commands confirmed that diamorphine.ko is a 64-bit Linux kernel module and exposed useful strings such as the challenge name and hints about hidden functionality.

Reverse Engineering the Module

r2 -e bin.relocs.apply=true -A diamorphine.ko
Inside radare2 I enumerated functions and searched for backdoor logic:

afl | egrep -i 'hacked|getdents|kill'
s 0x0800002c0        # address of hacked_kill (example)
pdf                  # inspect function disassembly
The hacked_kill function revealed custom handling for signals 64 and 46 that modify the current process credentials and rootkit visibility.

Backdoor Behaviour
From the reversed logic:

kill -64 $$ – gives the calling process UID 0 (root).

kill -46 0 – toggles the rootkit’s hide/unhide behaviour for processes and the module.

This confirmed that the module is a customized Diamorphine rootkit adapted for the challenge.

Connecting to the Challenge Instance
From the Cyberpsychosis challenge page I used the host and port shown under Download Files / Instance Info:

cd ~/Downloads/rev_cyberpsychosis
ncat <challenge-ip> <challenge-port>
This opened an unprivileged shell on the remote system.

Privilege Escalation Using the Backdoor
On the remote shell I triggered the signal-based backdoor:

id
kill -64 $$
id
whoami
After sending signal 64 to my own shell process, id and whoami showed that I had root privileges.

Disarming the Rootkit
Once I was root, I unloaded the malicious module:

lsmod | grep diamorphine
rmmod diamorphine
lsmod | grep diamorphine
The second lsmod confirmed that the module was no longer loaded.

Finding and Reading the Flag
find / -type f -name "*.txt" 2>/dev/null
cat /opt/psychosis/flag.txt
The flag file under /opt/psychosis/flag.txt contained the final answer for the challenge.

Final Flag
HTB{N0w_Y0u_C4n_S33_m3_4nd_th3_r00tk1t_h4s_b33n_sUcc3ssfully_d3f34t3d!!}
