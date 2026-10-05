# Wappalyzer-CLI-on-Linux

wappalyzer-next uses Playwright's Chromium, so Firefox, geckodriver and the Mozilla repo aren't needed.

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

Run this as your normal user, without `sudo`. The browser is saved per user in `~/.cache/ms-playwright`, and `sudo` would put it in `/root/.cache` instead. Calling it through the venv's own Python makes sure the Chromium build matches the Playwright version Wappalyzer uses.

```bash
~/.local/share/pipx/venvs/wappalyzer/bin/python -m playwright install chromium
```

Verify the download. You should see a `chromium-XXXX` folder:

```bash
ls ~/.cache/ms-playwright/
```

If `~/.local/share/pipx/venvs` doesn't exist, find your pipx venv folder with `pipx environment --value PIPX_LOCAL_VENVS` and use `<that folder>/wappalyzer/bin/python` instead.

## Step 4: Install Chromium's system libraries (choose your distro)

**Debian / Kali / Parrot / Ubuntu and derivatives**

```bash
sudo ~/.local/share/pipx/venvs/wappalyzer/bin/python -m playwright install-deps chromium
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

## Troubleshooting

**"Executable doesn't exist at ~/.cache/ms-playwright/chromium-XXXX":** The downloaded Chromium build doesn't match, or it was installed as a different user. Clear it and reinstall as your normal user:

```bash
rm -rf ~/.cache/ms-playwright
~/.local/share/pipx/venvs/wappalyzer/bin/python -m playwright install chromium
```

**After upgrading Wappalyzer** (`pipx upgrade wappalyzer`): Playwright may want a newer Chromium build. Rerun Step 3.

**Multiple users:** Each user who runs Wappalyzer needs to install the CLI (Step 2) and download Chromium (Step 3) under their own account.

**Alpine:** Playwright's bundled Chromium is built for glibc and won't run on Alpine's musl. Use `--scan-type fast` there, or run the tool in a Debian or Ubuntu container.

**No pipx package:** Install it with `python3 -m pip install --user pipx`. If you get an "externally-managed-environment" error, add `--break-system-packages`.

## Optional cleanup from the old Firefox method

```bash
sudo rm -f /usr/local/bin/geckodriver
sudo apt remove --purge firefox        # only if you don't use Firefox otherwise
sudo rm -f /etc/apt/sources.list.d/mozilla.list /etc/apt/preferences.d/mozilla /etc/apt/keyrings/packages.mozilla.org.asc
sudo apt update
```
