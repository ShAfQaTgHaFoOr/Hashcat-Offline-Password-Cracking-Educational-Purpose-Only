

# ⚡ Hashcat Lab: High-Performance Offline Cracking

An educational lab environment demonstrating high-performance, hardware-accelerated password cracking, target benchmarking, and structured mask attacks using Hashcat.

> **⚠️ Disclaimer:** This repository is strictly for educational and authorized testing purposes. Only run these exercises on your local machine, a dedicated virtual machine, a Docker container, or authorized lab environments (e.g., TryHackMe, Hack The Box). Never execute hardware-intensive cracking routines on unapproved systems.

---

## 🛠️ Environment Setup & Lab Execution

Follow these steps sequentially within your terminal environment to analyze hardware compatibility, structure target data, and perform advanced cracking routines.

### Part 1: Architecture Check & Setup

#### 1. Update Packages & Install Hashcat

Ensure your local package repository index is fresh and install Hashcat.

```bash
sudo apt update && sudo apt install -y hashcat

```

#### 2. Identify Compute Devices

Query Hashcat to list available hardware backends (OpenCL/CUDA engines, GPUs, or host CPUs). If a dedicated GPU is missing, ensure appropriate kernel headers and proprietary drivers are installed, or proceed with CPU runtimes.

```bash
hashcat -I

```

---

### Part 2: Target Generation & Dictionary Attack

#### 3. Establish Attack Data & Hash Infrastructure

Generate a micro-wordlist from the native Kali dictionary repository, append the verification key string (`password123`), and compute the raw target MD5 signature.

```bash
# Extract top entries and seed verification key
head -n 1000 /usr/share/wordlists/rockyou.txt > ~/rockyou-1000.txt
echo "password123" >> ~/rockyou-1000.txt

# Calculate baseline MD5 hash signature and display file output
echo -n "password123" | md5sum | awk '{print $1}' > ~/hashcat-hashes.txt
cat ~/hashcat-hashes.txt

```

#### 4. Execute Standard Dictionary Attack (Mode 0)

Launch a direct vocabulary correlation attack by passing the hash list through the tailored candidate file.

```bash
hashcat -m 0 -a 0 ~/hashcat-hashes.txt ~/rockyou-1000.txt

```

#### 5. Verify the Session Potfile Ledger

Once a match occurs, extract plaintext credentials directly from Hashcat's background tracking file (`hashcat.potfile`) without recalculating the keyspace.

```bash
hashcat --show -m 0 ~/hashcat-hashes.txt

```

---

### Part 3: Advanced Cracking Methodologies

#### 6. Execute a Targeted Mask Attack (Mode 3)

When exact wordlists fail but password composition patterns are hypothesized, use structured pattern masking. The string below scans for 4 lowercase letters followed by 2 digits (`?l?l?l?l?d?d`).

```bash
hashcat -m 0 -a 3 ~/hashcat-hashes.txt "?l?l?l?l?d?d"

```

#### 7. Apply Rule Engine Rule Mutations

Incorporate rule vectors to modify basic dictionary values on the fly (adding capitalization layers, trailing punctuation markers, or common numbers).

```bash
hashcat -m 0 -a 0 ~/hashcat-hashes.txt ~/rockyou-1000.txt -r /usr/share/hashcat/rules/best64.rule

```

#### 8. Run Hardware Hash Benchmarks

Benchmark your underlying compute hardware across various cryptography protocols to calculate exact operations per second (H/s).

```bash
hashcat -b

```

---

## 📋 Attack Configuration Reference

| Flag Syntax | Configuration Function |
| --- | --- |
| `-m 0` | Sets the target cryptographic profile to **MD5**. |
| `-a 0` | Configures the deployment profile to **Straight / Dictionary Mode**. |
| `-a 3` | Configures the deployment profile to **Mask / Brute-Force Mode**. |
| `-r [path]` | Loads structural mutations rulesets (`best64.rule`) to expand dictionary variations. |
| `--show` | Queries local session ledgers to reveal previously deciphered token values. |

---

Made with ❤️ by **The Shafqat Ghafoor** — Happy Hacking!
