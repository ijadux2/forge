# Forge - A Home-Manager Like Tool for Fedora

A lightweight configuration management tool for Fedora that provides dotfile management and package installation functionality through a centralized TOML configuration.

## Overview

Forge is a bash script that helps you manage your Fedora system configuration by:
- Installing and managing system packages via dnf
- Managing dotfiles through symlinks to actual files
- Centralized configuration via a single `forge.toml` file
- User-level configuration similar to home-manager/nixos
- Automatic backup of existing files before replacement

## Installation

1. Clone or download this repository
2. Make the script executable:
   ```bash
   chmod +x forge
   ```
3. Optionally, move it to a directory in your PATH:
   ```bash
   sudo mv forge /usr/local/bin/forge
   ```

## Quick Start

```bash
# Initialize the configuration structure
./forge init

# Edit the configuration file to add packages and dotfiles
nano ~/.config/forge/forge.toml

# Apply configuration
./forge switch

# Check status
./forge status
```

## Commands

### `init`
Initialize the configuration structure and create `forge.toml`.

```bash
./forge init
```

Creates:
- `~/.config/forge/` - Main configuration directory
- `~/.config/forge/forge.toml` - Central configuration file

### `switch`
Apply the current configuration (install packages, deploy dotfiles).

```bash
./forge switch
```

This command:
1. Installs all packages listed in `forge.toml`
2. Creates symlinks for all configured dotfiles
3. Creates backups of existing files before replacing them
4. Updates the last switch timestamp

### `status`
Show current configuration status and information.

```bash
./forge status
```

Displays:
- Configuration directory paths
- Last switch timestamp
- List of configured packages and dotfiles

### `help`
Show help message with all available commands.

```bash
./forge help
```

## Configuration

### forge.toml
The central configuration file located at `~/.config/forge/forge.toml`:

```toml
# Forge Configuration File
# User-level configuration similar to home-manager/nixos

[forge]
version = "1.0"
last_switch = null

[packages]
# List of packages to install using dnf
# Just list package names - presence means install, absence means don't install
git
vim
curl
wget

[dotfiles]
# Dotfiles to manage with symlinks
# Format: "target_path" = "source_path"
# target_path: where the symlink should be created (relative to home directory)
# source_path: where the actual file is stored (relative to forge directory)
".bashrc" = "dotfiles/.bashrc"
".config/vimrc" = "dotfiles/vimrc"
".config/alacritty/alacritty.yml" = "dotfiles/alacritty.yml"

[options]
# Additional options
backup = true
backup_dir = "backup"
```

### Configuration Sections

#### `[forge]`
- `version`: Configuration file version
- `last_switch`: Timestamp of last switch operation (auto-updated)

#### `[packages]`
List of packages to install via dnf. **Just list package names** - presence means install, absence means don't install.

**Examples:**
```toml
[packages]
git
vim
curl
wget
nodejs
npm
```

To remove a package, simply delete the line containing the package name.

#### `[dotfiles]`
Dotfile mappings using symlinks:
- **Key**: Target path where symlink should be created (relative to home directory)
- **Value**: Source path where actual file is stored (relative to forge directory)

#### `[options]`
Additional configuration options:
- `backup`: Enable/disable backup of existing files (default: true)
- `backup_dir`: Directory name for backups (relative to forge directory)

## File Structure

```
~/.config/forge/
├── forge.toml          # Main configuration file
├── dotfiles/           # Your actual dotfiles (source files)
│   ├── .bashrc
│   ├── vimrc
│   └── alacritty.yml
└── backup/            # Backups of replaced files
    ├── .bashrc.20231121_143022.bak
    └── vimrc.20231121_143022.bak
```

## How It Works

### Package Management
Packages are managed through the `[packages]` section in `forge.toml`:
- **Add package**: Simply add the package name on a new line
- **Remove package**: Delete the line containing the package name
- **No flags needed**: Just presence/absence of the package name matters

### Dotfile Management
Forge uses symlinks to manage dotfiles:
1. Your actual dotfiles are stored in `~/.config/forge/dotfiles/`
2. Symlinks are created from your home directory to these files
3. This allows you to version control your dotfiles in one place
4. Changes to the source files are immediately reflected in your home directory

### Backup System
Before creating symlinks, Forge:
1. Checks if the target file exists and is not a symlink
2. Creates a timestamped backup in the backup directory
3. Removes the original file
4. Creates the symlink to your managed dotfile

## Examples

### Basic Setup
```bash
# Initialize forge
./forge init

# Edit forge.toml to add packages
nano ~/.config/forge/forge.toml

# Add to [packages] section:
git
vim
curl

# Create your dotfiles directory and add files
mkdir -p ~/.config/forge/dotfiles
echo "export EDITOR=vim" > ~/.config/forge/dotfiles/.bashrc

# Add to [dotfiles] section:
".bashrc" = "dotfiles/.bashrc"

# Apply configuration
./forge switch
```

### Managing Application Configurations
```bash
# Add alacritty configuration
mkdir -p ~/.config/forge/dotfiles/.config/alacritty
cp ~/.config/alacritty/alacritty.yml ~/.config/forge/dotfiles/.config/alacritty/

# Edit forge.toml
nano ~/.config/forge/forge.toml

# Add to [dotfiles] section:
".config/alacritty/alacritty.yml" = "dotfiles/.config/alacritty/alacritty.yml"

# Apply changes
./forge switch
```

### Package Management Examples
```toml
[packages]
# Development tools
git
vim
nodejs
npm

# System utilities
curl
wget
tree
htop

# To remove a package, just delete the line
# For example, to remove htop, delete the "htop" line
```

### Version Control Your Configuration
```bash
# Initialize git repository in forge directory
cd ~/.config/forge
git init
git add .
git commit -m "Initial configuration"

# Now you can version control your entire system configuration
git add forge.toml dotfiles/
git commit -m "Updated vim configuration"
```

## Environment Variables

- `FORGE_DIR`: Override the default configuration directory (default: `~/.config/forge`)

## Dependencies

- `dnf` - Fedora package manager
- `sed` - For updating configuration file (usually pre-installed)

## Migration from Previous Version

If you were using the old version of forge with `git = true` format:

1. Your existing configuration will not be automatically migrated
2. Run `./forge init` to create the new `forge.toml` structure
3. Convert your package configuration from:
   ```toml
   # Old format
   git = true
   vim = true
   ```
   to:
   ```toml
   # New format
   git
   vim
   ```
4. Move your existing dotfiles from the old directory to `~/.config/forge/dotfiles/`

## License

This project is open source. Feel free to contribute or report issues.