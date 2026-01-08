# Windows Installation

## PowerShell Install

Run in PowerShell (as Administrator):

```powershell
powershell -c "irm https://muxi.org/install | iex"
```

This installs to `%LOCALAPPDATA%\MUXI\bin` and adds it to your PATH.

## Verify Installation

Open a new PowerShell window:

```powershell
muxi --version
muxi-server version
```

## Manual Install

1. Download from [GitHub Releases](https://github.com/muxi-ai/server/releases)
2. Extract to `C:\Program Files\MUXI\`
3. Add to PATH:
   - Open System Properties → Environment Variables
   - Add `C:\Program Files\MUXI\` to PATH

## Configuration

Config files are stored in `%USERPROFILE%\.muxi\`:

```
%USERPROFILE%\.muxi\
├── server\
│   └── config.yaml
└── cli\
    ├── config.yaml
    └── servers.yaml
```

## Running as a Service

Use NSSM (Non-Sucking Service Manager):

```powershell
# Install NSSM
choco install nssm

# Create service
nssm install MUXIServer "C:\Program Files\MUXI\muxi-server.exe" serve

# Start service
nssm start MUXIServer
```

Or use Windows Task Scheduler to run on startup.

## WSL Alternative

For a Linux-like experience on Windows, use WSL:

```powershell
# Install WSL
wsl --install

# In WSL terminal
curl -fsSL https://muxi.org/install | sudo bash
```

## Firewall

Allow port 7890:

```powershell
New-NetFirewallRule -DisplayName "MUXI Server" -Direction Inbound -Port 7890 -Protocol TCP -Action Allow
```

## Troubleshooting

### "muxi" is not recognized

Restart PowerShell or run:

```powershell
$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")
```

### Execution Policy Error

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

## Next Steps

- [Quickstart](../quickstart.md) - Create your first formation
- [Server Configuration](../server/configuration.md) - Customize settings
