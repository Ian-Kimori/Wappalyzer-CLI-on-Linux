# Wappalyzer-CLI-on-Linux

wappalyzer-next now uses Playwright's Chromium, so Firefox, geckodriver and the Mozilla repo are no longer needed.

## Step 1: Install pipx and tools (choose your distro)

**Debian / Kali / Parrot / Ubuntu / Linux Mint / Pop!_OS / Zorin / elementary**

```bash
sudo apt update
sudo apt install -y pipx curl
```

**Fedora**

```bash
sudo dnf install -y pipx curl
```

**RHEL / Rocky / AlmaLinux / CentOS Stream** (pipx comes from EPEL)

```bash
sudo dnf install -y epel-release
sudo dnf install -y pipx curl
```

**Arch / Manjaro / EndeavourOS / Garuda**

```bash
sudo pacman -Syu --needed python-pipx curl
```

**openSUSE (Tumbleweed / Leap)**

```bash
sudo zypper refresh
sudo zypper install -y python3-pipx curl
```

If `python3-pipx` isn't found, run `zypper search pipx` and install the versioned name it shows, such as `python311-pipx`.

## Step 2: Install the Wappalyzer CLI (all distros)

```bash
pipx install wappalyzer
pipx ensurepath
source ~/.bashrc          # or: source ~/.zshrc
```

## Step 3: Download Playwright's Chromium (all distros)

```bash
~/.local/share/pipx/venvs/wappalyzer/bin/playwright install chromium
```

If that path doesn't exist, find your pipx venv folder with `pipx environment --value PIPX_LOCAL_VENVS` and use `<that folder>/wappalyzer/bin/playwright` instead.

## Step 4: Install Chromium's system libraries (choose your distro)

**Debian / Kali / Parrot / Ubuntu and derivatives**

```bash
sudo ~/.local/share/pipx/venvs/wappalyzer/bin/playwright install-deps chromium
```

**Fedora / RHEL / Rocky / AlmaLinux** (installing the system Chromium pulls in every library Playwright needs; on RHEL-family it comes from EPEL)

```bash
sudo dnf install -y chromium
```

**Arch and derivatives**

```bash
sudo pacman -S --needed chromium
```

**openSUSE**

```bash
sudo zypper install -y chromium
```

## Step 5: Test it

```bash
wappalyzer -i https://example.com
```

**Usage**

```bash
wappalyzer -i https://example.com                      # default scan
wappalyzer -i https://example.com --scan-type fast     # HTTP only, no browser
wappalyzer -i https://example.com --scan-type full     # most accurate, renders JS
wappalyzer -i urls.txt -t 10 -oJ results.json          # bulk scan, JSON output
```

## Notes

**Alpine:** Playwright's bundled Chromium is built for glibc and won't run on Alpine's musl. Use `--scan-type fast` there, or run the tool in a Debian or Ubuntu container.

**No pipx package:** Install it with `python3 -m pip install --user pipx`. If you get an "externally-managed-environment" error, add `--break-system-packages`.

**Optional cleanup on your machine:** If you want to drop what the old Firefox method installed, run:

```bash
sudo rm -f /usr/local/bin/geckodriver
sudo apt remove --purge firefox        # only if you don't use Firefox otherwise
sudo rm -f /etc/apt/sources.list.d/mozilla.list /etc/apt/preferences.d/mozilla /etc/apt/keyrings/packages.mozilla.org.asc
sudo apt update
```
