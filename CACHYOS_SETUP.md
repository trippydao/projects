# CachyOS Setup Guide

**Date:** 2025-10-07
**Purpose:** Sync Projects directory and PARA structure to CachyOS machine

## Prerequisites

1. SSH key set up on CachyOS and added to GitHub
2. Git installed: `sudo pacman -S git`

## One-Time Setup on CachyOS

### 1. Configure Git

```bash
git config --global user.name "Trippydao"
git config --global user.email "jon-erik@aurelia.fund"
```

### 2. Setup SSH Key (if not already done)

```bash
# Option A: Generate new key
ssh-keygen -t ed25519 -C "jon-erik@aurelia.fund"
cat ~/.ssh/id_ed25519.pub  # Copy output

# Add to: https://github.com/settings/ssh/new

# Test connection
ssh -T git@github.com
```

### 3. Create PARA Structure

```bash
mkdir -p ~/Projects ~/Areas ~/Resources ~/Archive
```

### 4. Clone Repos

```bash
cd ~/Projects
git clone git@github.com:trippydao/dotfiles.git
git clone git@github.com:trippydao/crypto-tools.git
git clone git@github.com:trippydao/utilities.git
```

### 5. Setup Symlinks

```bash
cd ~/Projects/utilities
./setup-symlinks.sh
```

This will:
- Create `~/.local/bin/mcp-manager`
- Create `~/mcp-configs` → `~/Projects/utilities/mcp-configs`
- Add `~/.local/bin` to PATH (if needed)

### 6. Verify Setup

```bash
# Check if mcp-manager is accessible
mcp-manager --help

# Check symlinks
ls -la ~ | grep mcp-configs
```

## Daily Sync Workflow

### Before Starting Work

Pull latest changes from all repos:

```bash
cd ~/Projects/dotfiles && git pull
cd ~/Projects/crypto-tools && git pull
cd ~/Projects/utilities && git pull
```

Or use this one-liner:

```bash
for repo in dotfiles crypto-tools utilities; do
  cd ~/Projects/$repo && echo "Updating $repo..." && git pull
done
```

### After Making Changes

Commit and push changes:

```bash
cd ~/Projects/[repo]
git add .
git commit -m "Description of changes"
git push
```

## PARA Structure

```
~/
├── Projects/              # Active code projects (Git repos)
│   ├── dotfiles/
│   ├── crypto-tools/
│   ├── utilities/
│   └── learning/
├── Areas/                 # Ongoing responsibilities
├── Resources/             # Reference materials
└── Archive/               # Completed/old items
```

## macOS to CachyOS Mapping

| macOS | CachyOS | Sync Method |
|-------|---------|-------------|
| `~/Projects/` | `~/Projects/` | Git (GitHub) |
| `~/Obsidian/` | `~/Obsidian/` | Obsidian Sync or Dropbox |
| `~/Dropbox/` | Not synced | Use Google Drive or consolidate |
| `~/Library/CloudStorage/GoogleDrive-*/` | Not synced | Access via web or rclone |

## Repositories

- **dotfiles**: https://github.com/trippydao/dotfiles (private)
- **crypto-tools**: https://github.com/trippydao/crypto-tools (private)
- **utilities**: https://github.com/trippydao/utilities (private)

## Notes

- All repos are **private** for security
- Use SSH (not HTTPS) for authentication
- Symlinks keep tools accessible in expected locations
- `~/.local/bin` is standard Linux location for user binaries
- Add `export PATH="$HOME/.local/bin:$PATH"` to `~/.bashrc` if needed

## Troubleshooting

### "Permission denied (publickey)"
- Check SSH key: `ssh -T git@github.com`
- Ensure key is added to GitHub: https://github.com/settings/keys

### "mcp-manager: command not found"
- Check symlink: `ls -la ~/.local/bin/mcp-manager`
- Check PATH: `echo $PATH | grep .local/bin`
- Run setup script again: `cd ~/Projects/utilities && ./setup-symlinks.sh`

### Merge conflicts
```bash
# If you have uncommitted changes
git stash
git pull
git stash pop

# If conflict, resolve manually then:
git add .
git commit -m "Resolve merge conflict"
git push
```

## Future Enhancements

- Add automation script for daily pull/push
- Setup pre-commit hooks for consistent commits
- Add aliases for common git operations
- Consider using `mr` (myrepos) for multi-repo management
