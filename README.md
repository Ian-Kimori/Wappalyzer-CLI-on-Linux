# Wappalyzer-CLI-on-Linux

The official open-source Wappalyzer CLI was discontinued in 2023 when Wappalyzer went commercial, so the old `npm install -g wappalyzer` package is deprecated and often breaks. These are the working options now.

## Option 1: wappalyzer-next (closest to the original)

This is a maintained Python rewrite by s0md3v that uses Wappalyzer's fingerprints.

**Step 1: Install pipx**

```bash
sudo apt update
sudo apt install pipx
```

**Step 2: Install Firefox** (needed for full and balanced scans)

```bash
sudo apt install firefox-esr          # Debian/Kali
# sudo apt install firefox            # Ubuntu
```

**Step 3: Install geckodriver**

```bash
GECKO_VER=$(curl -s https://api.github.com/repos/mozilla/geckodriver/releases/latest | grep -oP '"tag_name": "\K[^"]+')
wget https://github.com/mozilla/geckodriver/releases/download/${GECKO_VER}/geckodriver-${GECKO_VER}-linux64.tar.gz
tar -xzf geckodriver-${GECKO_VER}-linux64.tar.gz
sudo mv geckodriver /usr/local/bin/
sudo chmod +x /usr/local/bin/geckodriver
rm geckodriver-${GECKO_VER}-linux64.tar.gz
geckodriver --version
```

**Step 4: Install wappalyzer**

```bash
pipx install wappalyzer
pipx ensurepath
source ~/.bashrc                      # or: source ~/.zshrc
```

**Usage**

```bash
wappalyzer -i https://example.com
wappalyzer -i https://example.com --scan-type fast     # no browser, HTTP only
wappalyzer -i urls.txt -t 10 -oJ results.json          # bulk, JSON output
```

## Option 2: webanalyze (fast Go binary)

This works well for bulk recon.

**Install Go and set PATH** (skip if you already have Go)

```bash
sudo apt install golang-go
echo 'export PATH=$PATH:$HOME/go/bin' >> ~/.bashrc    # or ~/.zshrc
source ~/.bashrc
```

**Install and use webanalyze**

```bash
go install -v github.com/rverton/webanalyze/cmd/webanalyze@latest
webanalyze -update                     # downloads technologies.json
webanalyze -host https://example.com -crawl 1
webanalyze -hosts hosts.txt -output json
```

## Option 3: httpx tech detection

If you already use ProjectDiscovery tools, httpx includes Wappalyzer fingerprints through wappalyzergo. It needs Go, so do the Go setup from Option 2 first.

```bash
go install -v github.com/projectdiscovery/httpx/cmd/httpx@latest
echo example.com | httpx -td
```

## Legacy npm package (not recommended)

```bash
sudo apt install nodejs npm
sudo npm install -g wappalyzer    # deprecated; needs Chromium, often fails
```

For recon pipelines, `httpx -td` is usually the most convenient. For the most accurate single-target detection, including JS-rendered tech, use wappalyzer-next with `--scan-type full`.
