# Wappalyzer-CLI-on-Linux

The Firefox and pipx install differs by distro. The geckodriver and Wappalyzer steps are the same everywhere.

## Step 1: Firefox, pipx and tools (choose your distro)

**Debian / Kali / Parrot**

```bash
sudo apt update
sudo apt install -y firefox-esr pipx xdg-utils curl wget tar
```

**Ubuntu / Linux Mint / Pop!_OS / Zorin / elementary**

Ubuntu's `firefox` is a Snap stub that conflicts with geckodriver, so install Mozilla's `.deb` instead:

```bash
sudo apt update
sudo apt install -y pipx xdg-utils curl wget tar
sudo snap remove firefox 2>/dev/null

sudo install -d -m 0755 /etc/apt/keyrings
wget -q https://packages.mozilla.org/apt/repo-signing-key.gpg -O- | sudo tee /etc/apt/keyrings/packages.mozilla.org.asc > /dev/null
echo "deb [signed-by=/etc/apt/keyrings/packages.mozilla.org.asc] https://packages.mozilla.org/apt mozilla main" | sudo tee /etc/apt/sources.list.d/mozilla.list > /dev/null
echo '
Package: *
Pin: origin packages.mozilla.org
Pin-Priority: 1000
' | sudo tee /etc/apt/preferences.d/mozilla

sudo apt update
sudo apt install -y firefox
```

**Fedora**

```bash
sudo dnf install -y firefox pipx xdg-utils curl wget tar
```

**RHEL / Rocky / AlmaLinux / CentOS Stream** (pipx comes from EPEL)

```bash
sudo dnf install -y epel-release
sudo dnf install -y firefox pipx xdg-utils curl wget tar
```

**Arch / Manjaro / EndeavourOS / Garuda**

```bash
sudo pacman -Syu --needed firefox python-pipx xdg-utils curl wget tar geckodriver
```

geckodriver is in Arch's repos, so skip Step 2.

**openSUSE (Tumbleweed / Leap)**

```bash
sudo zypper refresh
sudo zypper install -y firefox python3-pipx xdg-utils curl wget tar
```

If `python3-pipx` isn't found, run `zypper search pipx` and install the versioned name it shows, such as `python311-pipx`.

**Alpine**

```bash
sudo apk add firefox py3-pipx xdg-utils curl wget tar geckodriver
```

geckodriver is in Alpine's repos (Mozilla's binary doesn't run on musl), so skip Step 2.

**Verify Firefox**

```bash
firefox --version
```

## Step 2: geckodriver (all distros except Arch and Alpine)

This detects x86_64 or ARM64 automatically:

```bash
ARCH=$(uname -m)
case "$ARCH" in
  x86_64)  GECKO_ARCH="linux64" ;;
  aarch64) GECKO_ARCH="linux-aarch64" ;;
  *) echo "Unsupported architecture: $ARCH"; exit 1 ;;
esac

GECKO_VER=$(curl -s https://api.github.com/repos/mozilla/geckodriver/releases/latest | grep -oP '"tag_name": "\K[^"]+')
wget https://github.com/mozilla/geckodriver/releases/download/${GECKO_VER}/geckodriver-${GECKO_VER}-${GECKO_ARCH}.tar.gz
tar -xzf geckodriver-${GECKO_VER}-${GECKO_ARCH}.tar.gz
sudo mv geckodriver /usr/local/bin/
sudo chmod +x /usr/local/bin/geckodriver
rm geckodriver-${GECKO_VER}-${GECKO_ARCH}.tar.gz
geckodriver --version
```

## Step 3: Wappalyzer CLI (all distros)

```bash
pipx install wappalyzer
pipx ensurepath
source ~/.bashrc          # or: source ~/.zshrc
```

## Step 4: Test it

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

If your distro doesn't package pipx, install it with `python3 -m pip install --user pipx`. On distros that block this with an "externally-managed-environment" error, add `--break-system-packages`.
