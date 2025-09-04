# EagleEye Domain Name Updater

EagleEye is a powerful command-line tool for scanning a codebase to find and replace hard-coded domain names and URLs. It's designed to be safe, with a default "dry-run" mode, and flexible, with numerous options to control its behavior.

## Features

*   **Safe by Default:** Runs in "dry-run" mode by default, showing you what changes would be made without actually modifying any files.
*   **Flexible Mapping:** Define your domain and URL mappings in a simple, easy-to-read `domains.map` file.
*   **Configurable:** Specify which files to scan by extension, and which directories to exclude.
*   **Detailed Logging:** Generates a comprehensive digest log of all intended or executed replacements.
*   **Backup Option:** Can create backups of modified files.
*   **Interactive or CLI-driven:** Can be run interactively, or with command-line arguments for automation.

## Installation

1.  Clone this repository or download the files.
2.  Place the `scripts` directory containing `eagleeye-domain-update.sh` and `domains.map` into your project.

## Configuration

The `domains.map` file is where you define the URLs to be replaced.

*   **Format:** `OLD_URL => NEW_URL` or `OLD_URL NEW_URL`
*   **Comments:** Lines starting with `#` are ignored.
*   **Order Matters:** Place more specific URLs before less specific ones. For example, `sub.domain.com` should come before `domain.com`.

### Example `domains.map`

```
# EagleEye Domain/URL migration tool
# Format: OLD => NEW
# Order: most specific → least specific

# ========= Legacy → Canonical =========
https://www.sub1.example-old.com => https://www.sub1.example.new.com
http://www.sub1.example-old.com  => https://www.sub1.example.new.com
https://sub1.example-old.com      => https://sub1.example.new.com
http://sub1.example-old.com       => https://sub1.example.new.com
https://www.example-old.com         => https://www.example.new.com
http://www.example-old.com          => https://www.example.new.com
https://example-old.com             => https://example.new.com
http://example-old.com              => https://example.new.com

# ========= Enforce HTTPS on the NEW canonical hosts =========
http://www.sub1.example.new.com  => https://www.sub1.example.new.com
http://sub1.example.new.com      => https://sub1.example.new.com
http://www.example.new.com          => https://www.example.new.com
http://example.new.com              => https://example.new.com
```

## Usage

Run the script from your terminal.

### Interactive Mode

If you run the script without any options, it will prompt you for the website's root directory and the relative path to the script.

```bash
./scripts/eagleeye-domain-update.sh
```

### Command-line Options

You can also use command-line options to control the script's behavior.

```
Usage: eagleeye-domain-update.sh [options]

Options:
  -r <root>        Root directory to scan (overrides interactive root)
  -m <mapfile>     Mapping file path (default: ./scripts/domains.map if present)
  -i "<inline>"    Inline mappings, comma-separated, e.g.:
                     "http://old.com=>https://new.com,old2=>new2"
  -t <tags>        Extension tags (comma-separated). Union of sets. Examples:
                     -t html
                     -t web
                     -t templates,backend
                   Use --tags=? to list sets. Overridden by -E.
  --tags=<tags>    Same as -t; --tags=? lists sets and exits.
  -E <regex>       File extension regex (overrides -t). Default: html?
  -x <dir>         Additional directory name to exclude (repeatable)
  -l <logfile>     Digest log output (default: ./scripts/domain-update.digest.log)
  -e               Execute replacements (default: dry-run)
  -n               Force dry-run (overrides -e)
  -b <ext>         Keep per-file backup with this extension (e.g. .bak)
  -h               Help
```

### Examples

**Dry-run, scanning only HTML files in `/var/www/html`:**

```bash
./scripts/eagleeye-domain-update.sh -r /var/www/html
```

**Execute replacements, scanning HTML, CSS, and JS files:**

```bash
./scripts/eagleeye-domain-update.sh -r /var/www/html -t web -e
```

**Execute replacements and create backups with a `.bak` extension:**

```bash
./scripts/eagleeye-domain-update.sh -r /var/www/html -t web -e -b .bak
```

**Use a different map file:**

```bash
./scripts/eagleeye-domain-update.sh -r /var/www/html -m /path/to/another.map
```

## Digest Log

The script generates a `domain-update.digest.log` file in the `scripts` directory. This file contains detailed information about the scan and replacements.

*   **Header:** Shows the run details, including whether it was a dry-run or an execution, the root directory, the map file used, and other configuration options.
*   **Stats:** Provides a summary of the number of files scanned, files with hits, and total occurrences.
*   **By-resource ranking:** A list of files with the most replacements.
*   **Per-file section:** A detailed breakdown of each file with replacements, including the line and column number of each change.

## License

This project is licensed under the terms of the license specified in the `.license` or `.license.md` file.
