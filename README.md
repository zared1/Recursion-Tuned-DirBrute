# Recursion-Tuned-DirBrute

A threaded directory/URL checker with optional recursive link discovery and jitter to avoid aggressive scanning. It reads paths from a wordlist, queries `domain + path`, prints basic page info (URL, title, length, status code), and — if enabled — follows links found on pages (recursively, but depth-limited by design because recursion is only enabled for the initial pass).

## Features

* Multithreaded HTTP requests using `requests`
* Optional recursive link discovery using `BeautifulSoup`
* Jitter (sleep) between requests to reduce detection/noise
* Prints page metadata: final URL, `<title>`, content length, and HTTP status code
* Simple CLI with options for threads, jitter, and recursion

## Requirements

* Python 3.8+ (recommended)
* `requests`
* `beautifulsoup4`

Install dependencies:

```bash
pip install requests beautifulsoup4
```

## How to Run

```bash
python Recursion-Tuned-DirBrute.py <domain> <wordlist> [--jitter J] [--threads N] [--recursive]
```

Examples:

```bash
# Basic scan
python Recursion-Tuned-DirBrute.py https://example.com ./wordlists/common.txt

# Faster scan with more threads and small jitter
python Recursion-Tuned-DirBrute.py https://example.com ./wordlists/common.txt --threads 20 --jitter 0.5

# Enable recursive link discovery (follow links found on pages)
python Recursion-Tuned-DirBrute.py https://example.com ./wordlists/common.txt --recursive
```

**Wordlist format:** one path or fragment per line. The script appends each line directly to the provided domain string; include slashes in your wordlist entries as needed (e.g., `admin/`, `robots.txt`).

## Notes & Caveats

* **Ethics & legality:** Only run this against targets you own or have explicit permission to test. Unauthorized scanning or crawling can be illegal.
* **Recursion behavior:** Recursive link following is simple and can revisit pages or cause deep crawling — use with care. It currently spawns recursive calls inline (not queued or deduped), which may create high request volumes.
* **Jitter:** Use `--jitter` to add delays between requests to reduce load and detection (value in seconds).
* **Threading model:** The script uses threads where each thread iterates over the full URL list; for large wordlists a queue-based worker model would be more efficient and avoid duplicate work.
* **Robustness:** Add request timeouts (e.g., `requests.get(..., timeout=...)`), retry logic, URL normalization, and deduplication to improve stability.
* **Politeness:** Respect `robots.txt`, rate limits, and target availability when scanning public sites.

## Suggested Improvements (if you want to iterate)

* Replace the `for url in urls` per-thread approach with a thread-safe queue so each path is checked exactly once.
* Add seen-URL deduplication and a recursion depth limit.
* Add per-request timeout and HTTP headers (User-Agent) and optional proxy support.
* Save results to a file (CSV/JSON) and support filtering by HTTP status codes.
* Add rate limiting and an optional `--respect-robots` flag.
