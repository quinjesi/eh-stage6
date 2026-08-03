# Root the Box: Exploit Chain as Code
---

## Overview

An exploit‑chain implementation for a vulnerable offline VM.


## Requirements

- Kali Linux (Attacker) 
- Python 3.11+, curl, ssh
- VirtualBox and Vagrant installed
- The target VM must be running on the host‑only network
---

## How to Run the Exploit Chain

1. **Start the lab**
	vagrant up 
	vagrant snapshot save clean-start

2. **Run test from the tests directory**
	pytest test_exploit.py -v

3. **Run the exploit code from the exploit-chain directory** 
	python3 exploit2.py --target 192.168.56.21 --phase full --output run_result.json

4. **Reliability Runs**
	for i in {1..5}; do
		vagrant snapshot restore vulnerable clean-start
		python3 exploit2.py --target 192.168.56.21 --phase full --output run_result.json
	done

5. **Negative Retest**
	python3 exploit2.py --target 192.168.56.22 --phase full --output negative.json

