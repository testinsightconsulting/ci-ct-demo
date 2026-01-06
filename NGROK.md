# ngrok Setup Guide

This guide will help you install and configure ngrok to expose your local HTTP server on port 8443 to the internet.

## What is ngrok?

ngrok creates a secure tunnel from a public endpoint to a locally running web service. This allows you to:
- Access your local server from anywhere on the internet
- Share your local development server with others
- Test webhooks and integrations that require a public URL
- Expose services running on specific ports (like port 8443)

## Prerequisites

- An internet connection
- A local HTTP server running on port 8443 (or any port you want to expose)
- Administrator/root privileges for installation (depending on your OS)

## Installation

### Windows

#### Method 1: Using Chocolatey (Recommended)

1. **Open PowerShell as Administrator** (Right-click PowerShell → "Run as Administrator")

2. **Install ngrok:**
   ```powershell
   choco install ngrok -y
   ```

3. **Verify installation:**
   ```powershell
   ngrok version
   ```

#### Method 2: Direct Download

1. **Download ngrok:**
   - Visit: https://ngrok.com/download
   - Download the Windows version (ZIP file)

2. **Extract the ZIP file:**
   - Extract `ngrok.exe` to a folder (e.g., `C:\ngrok`)

3. **Add to PATH (Optional but recommended):**
   - Open System Properties → Environment Variables
   - Add `C:\ngrok` to your PATH variable
   - Or use the full path: `C:\ngrok\ngrok.exe`

#### Method 3: Using winget (Windows 10/11)

```powershell
winget install ngrok
```

### macOS

#### Method 1: Using Homebrew (Recommended)

```bash
brew install ngrok/ngrok/ngrok
```

#### Method 2: Direct Download

1. **Download ngrok:**
   ```bash
   curl -O https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-darwin-amd64.zip
   ```

2. **Extract and install:**
   ```bash
   unzip ngrok-v3-stable-darwin-amd64.zip
   sudo mv ngrok /usr/local/bin/
   ```

### Linux

#### Method 1: Using Package Manager

**Ubuntu/Debian:**
```bash
curl -s https://ngrok-agent.s3.amazonaws.com/ngrok.asc | sudo tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null
echo "deb https://ngrok-agent.s3.amazonaws.com buster main" | sudo tee /etc/apt/sources.list.d/ngrok.list
sudo apt update && sudo apt install ngrok
```

**Arch Linux:**
```bash
yay -S ngrok
```

#### Method 2: Direct Download

```bash
curl -O https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-linux-amd64.zip
unzip ngrok-v3-stable-linux-amd64.zip
sudo mv ngrok /usr/local/bin/
```

## Getting Started

### Step 1: Create an ngrok Account

1. **Sign up for a free account:**
   - Visit: https://dashboard.ngrok.com/signup
   - Create an account (free tier available)

2. **Get your authtoken:**
   - After signing up, go to: https://dashboard.ngrok.com/get-started/your-authtoken
   - Copy your authtoken

### Step 2: Configure ngrok

**Authenticate ngrok with your account:**

```bash
ngrok config add-authtoken YOUR_AUTHTOKEN_HERE
```

Replace `YOUR_AUTHTOKEN_HERE` with the token you copied from the dashboard.

**Verify configuration:**
```bash
ngrok config check
```

### Step 3: Start Your Local Server

Before starting ngrok, make sure you have a server running on port 8443.

**Example: Starting a Python HTTP server on port 8443:**

```bash
# Python 3
python -m http.server 8443

# Or using a custom server
python api_server.py  # If configured to run on port 8443
```

**Example: Starting a Node.js server on port 8443:**

```javascript
const http = require('http');
const server = http.createServer((req, res) => {
  res.writeHead(200, {'Content-Type': 'text/plain'});
  res.end('Hello from port 8443!\n');
});
server.listen(8443, () => {
  console.log('Server running on http://localhost:8443');
});
```

### Step 4: Expose Port 8443 with ngrok

**Open a new terminal/command prompt** and run:

```bash
ngrok http 8443
```

You should see output like:

```
ngrok

Session Status                online
Account                       Your Name (Plan: Free)
Version                       3.x.x
Region                        United States (us)
Latency                       -
Web Interface                 http://127.0.0.1:4040
Forwarding                    https://abc123.ngrok-free.app -> http://localhost:8443

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
```

### Step 4: Expose Port 8443 with ngrok (with Powershell)

```

"C:\Users\<username>\ngrok.exe" http 8443

ngrok                                                                                                   (Ctrl+C to quit)

�  One gateway for every AI model. Available in early access *now*: https://ngrok.com/r/ai

Session Status                online
Account                       inti sanchez (Plan: Free)
Update                        update available (version 3.34.1, Ctrl-U to update)
Version                       3.22.1
Region                        Europe (eu)
Latency                       97ms
Web Interface                 http://127.0.0.1:4040
Forwarding                    https://460b6173b6ae.ngrok-free.app -> http://localhost:8443

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
```



**Important URLs:**
- **Forwarding URL**: `https://abc123.ngrok-free.app` - This is your public URL that forwards to `localhost:8443`
- **Web Interface**: `http://127.0.0.1:4040` - ngrok's web interface to inspect requests

### Step 5: Test Your Tunnel

1. **Keep ngrok running** in the terminal
2. **Access your server** using the forwarding URL:
   ```
   https://abc123.ngrok-free.app
   ```
3. **Monitor requests** at: `http://127.0.0.1:4040`

## Advanced Usage

### Custom Domain (Paid Plans)

If you have a paid ngrok plan, you can use a custom domain:

```bash
ngrok http 8443 --domain=your-custom-domain.ngrok.io
```

### Static Domain (Paid Plans)

Reserve a static domain for consistent URLs:

```bash
ngrok http 8443 --domain=my-static-domain.ngrok-free.app
```

### Custom Configuration File

Create `ngrok.yml` in your home directory (`~/.ngrok2/ngrok.yml` on Mac/Linux, `%USERPROFILE%\.ngrok2\ngrok.yml` on Windows):

```yaml
version: "2"
authtoken: YOUR_AUTHTOKEN_HERE
tunnels:
  port8443:
    proto: http
    addr: 8443
```

Then start with:
```bash
ngrok start port8443
```

### Background Mode (Linux/macOS)

Run ngrok in the background:

```bash
ngrok http 8443 > /dev/null 2>&1 &
```

Or use `nohup`:
```bash
nohup ngrok http 8443 &
```

### Windows Service (Advanced)

You can run ngrok as a Windows service using tools like NSSM (Non-Sucking Service Manager).

## Integration with This Project

### Exposing the API Server on Port 8443

If you want to expose the API server on port 8443 instead of the default port 8000:

1. **Modify the API server** to run on port 8443, or
2. **Use ngrok to forward port 8443 to port 8000:**

```bash
# If your API server runs on port 8000, but you want to expose it as 8443
ngrok http 8000 --host-header=localhost:8000
```

Or modify `api_server.py` to run on port 8443:

```python
if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8443)
```

Then expose it:
```bash
ngrok http 8443
```

### Exposing Grafana Dashboard

To expose the Grafana dashboard (running on port 3000) via ngrok:

```bash
ngrok http 3000
```

Access Grafana at the ngrok URL: `https://abc123.ngrok-free.app`

## Troubleshooting

### Issue: "Cannot find file at '..\\lib\ngrok\tools\ngrok.exe'"

**Solution (Windows):**
1. Run PowerShell as Administrator
2. Reinstall ngrok:
   ```powershell
   choco uninstall ngrok -y
   choco install ngrok -y
   ```
3. Or use the fix script provided:
   ```powershell
   .\fix-ngrok.ps1
   ```

### Issue: "command not found: ngrok"

**Solution:**
- Verify ngrok is installed: Check if `ngrok.exe` (Windows) or `ngrok` (Mac/Linux) exists
- Add ngrok to your PATH environment variable
- Use the full path to ngrok executable

### Issue: "ERR_NGROK_108" or "authtoken not configured"

**Solution:**
```bash
ngrok config add-authtoken YOUR_AUTHTOKEN_HERE
```

Get your authtoken from: https://dashboard.ngrok.com/get-started/your-authtoken

### Issue: "Port 8443 is already in use"

**Solution:**
1. Find what's using port 8443:
   ```bash
   # Windows
   netstat -ano | findstr :8443
   
   # Mac/Linux
   lsof -i :8443
   ```

2. Stop the process using that port, or use a different port:
   ```bash
   ngrok http 8080  # Use port 8080 instead
   ```

### Issue: "Tunnel session failed: Only paid plans may bind custom subdomains"

**Solution:**
- Free plans get random subdomains
- Upgrade to a paid plan for custom/static domains
- Or use the random subdomain provided (it changes each time you restart ngrok)

### Issue: Connection timeout or "502 Bad Gateway"

**Solution:**
1. Verify your local server is running on port 8443:
   ```bash
   # Test locally
   curl http://localhost:8443
   ```

2. Check firewall settings - allow connections on port 8443

3. Ensure ngrok is pointing to the correct port:
   ```bash
   ngrok http 8443  # Make sure this matches your server port
   ```

### Issue: "ngrok free account connection limit"

**Solution:**
- Free accounts have connection limits
- Upgrade to a paid plan for higher limits
- Or restart ngrok to reset the connection count

## Security Considerations

⚠️ **Important Security Notes:**

1. **Exposing localhost to the internet** makes your server accessible to anyone with the ngrok URL
2. **Use authentication** on your local server when exposing it via ngrok
3. **Don't expose production services** without proper security measures
4. **Free ngrok URLs are public** - anyone with the URL can access your service
5. **Consider using ngrok's IP restrictions** for additional security

## Useful Commands

```bash
# Check ngrok version
ngrok version

# Check configuration
ngrok config check

# View ngrok configuration
ngrok config edit

# List active tunnels
ngrok api tunnels list

# Stop ngrok (Ctrl+C in the terminal)
```

## Additional Resources

- **ngrok Documentation**: https://ngrok.com/docs
- **ngrok Dashboard**: https://dashboard.ngrok.com
- **ngrok Status**: https://status.ngrok.com
- **Community Support**: https://ngrok.com/support

## Quick Reference

```bash
# Basic usage - expose port 8443
ngrok http 8443

# With custom domain (paid plans)
ngrok http 8443 --domain=my-domain.ngrok.io

# With basic auth
ngrok http 8443 --basic-auth="username:password"

# Inspect requests (web UI)
# Open http://127.0.0.1:4040 in your browser
```

---

**Need Help?** If you encounter issues not covered here, check the [ngrok documentation](https://ngrok.com/docs) or the troubleshooting section above.


