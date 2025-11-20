# fedora-home-manager

A home-manager like script for Fedora that provides dotfile management and package installation functionality.

## Overview

fedora-home-manager is a bash script that helps you manage your Fedora system configuration by:
- Installing and managing system packages via dnf
- Deploying and managing dotfiles to your home directory
- Automatically cleaning up packages that are no longer needed
- Tracking state of managed files and packages

## Installation

1. Clone or download this repository
2. Make the script executable:
   ```bash
   chmod +x forge
   ```
3. Optionally, move it to a directory in your PATH:
   ```bash
   sudo mv forge /usr/local/bin/fedora-home-manager
   ```

## Quick Start

```bash
# Initialize the configuration structure
./forge init

# Add a dotfile to management
./forge add ~/.bashrc

# Add packages to install (edit the packages file)
echo "git = \"latest\"" >> ~/.config/fedora-home-manager/packages.toml
echo "vim = \"latest\"" >> ~/.config/fedora-home-manager/packages.toml

# Apply configuration
./forge switch
```

## Commands

### `init`
Initialize the configuration structure and create example files.

```bash
./forge init
```

Creates:
- `~/.config/fedora-home-manager/` - Main configuration directory
- `~/.config/fedora-home-manager/dotfiles/` - Directory for your dotfiles
- `~/.config/fedora-home-manager/packages.toml` - TOML file with packages to install
- `~/.config/fedora-home-manager/state.json` - State tracking file
- `~/.config/fedora-home-manager/backup/` - Backup directory for existing files

### `switch`
Apply the current configuration (install packages, deploy dotfiles, cleanup old packages).

```bash
./forge switch
```

This command:
1. Installs all packages listed in `packages.toml`
2. Uninstalls packages that are no longer in `packages.toml`
3. Deploys all dotfiles from the dotfiles directory to your home
4. Creates backups of existing files before overwriting
5. Updates the state tracking

### `build`
Alias for `switch` command.

```bash
./forge build
```

### `add <file>`
Add a file to dotfiles management.

```bash
./forge add ~/.bashrc
./forge add ~/.vimrc
./forge add ~/.config/alacritty/alacritty.yml
```

The file is copied to the dotfiles directory while maintaining its relative path structure.

### `remove <path>`
Remove a dotfile from management.

```bash
./forge remove .bashrc
./forge remove .config/alacritty/alacritty.yml
```

Optionally restores the original file from backup.

### `list`
List all managed files and packages.

```bash
./forge list
```

Shows:
- All dotfiles currently under management
- All packages that will be installed

### `status`
Show current status and configuration information.

```bash
./forge status
```

Displays:
- Configuration directory paths
- Last switch timestamp
- List of managed files and packages

### `help`
Show help message with all available commands.

```bash
./forge help
```

## Configuration Files

### packages.toml
List of Fedora packages to install in TOML format:
```toml
# Fedora packages configuration
# TOML format for better package management

[packages]
# Core development tools
git = "latest"
vim = "latest"
curl = "latest"
wget = "latest"

# Additional packages can be added with version specifications
# Example: package_name = "version" or "latest"
```

### dotfiles directory
Place your dotfiles in `~/.config/fedora-home-manager/dotfiles/` maintaining the same directory structure as in your home:

```
~/.config/fedora-home-manager/dotfiles/
├── .bashrc
├── .vimrc
└── .config/
    └── alacritty/
        └── alacritty.yml
```

### state.json
Internal state tracking file (managed automatically):
- Tracks installed packages for cleanup
- Records last switch timestamp
- Maintains list of managed files

## Features

### Automatic Package Cleanup
When you remove a package from `packages.toml` and run `switch`, the package will be automatically uninstalled from your system.

### Package State Tracking
Previously installed packages are tracked to enable cleanup operations.

### TOML Format Support
Packages are managed in TOML format for better organization and version support.

### Backup System
Before overwriting existing files, the script creates timestamped backups in the backup directory.

### State Tracking
Previously installed packages are tracked to enable intelligent cleanup operations.

## Environment Variables

- `HOME_MANAGER_DIR`: Override the default configuration directory (default: `~/.config/fedora-home-manager`)

## Dependencies

- `dnf` - Fedora package manager
- `jq` - JSON processor (optional, for enhanced state tracking)

## Examples

### Basic Setup
```bash
# Initialize
./forge init

# Add your shell configuration
./forge add ~/.bashrc
./forge add ~/.bash_profile

# Add development tools
cat >> ~/.config/fedora-home-manager/packages.toml << 'EOF'
git = "latest"
vim = "latest"
nodejs = "latest"
npm = "latest"
EOF

# Apply configuration
./forge switch
```

### Managing Configuration Files
```bash
# Add application configurations
./forge add ~/.config/alacritty/alacritty.yml
./forge add ~/.config/git/config
./forge add ~/.config/nvim/init.vim

# Check what's managed
./forge list

# Apply changes
./forge switch
```

### Removing Packages
```bash
# Edit packages.toml and remove entries for packages you don't want
# Then run switch to uninstall them
./forge switch
```

## File Structure

```
~/.config/fedora-home-manager/
├── dotfiles/          # Your dotfiles to deploy
├── packages.toml      # TOML file with packages to install
├── state.json         # State tracking
└── backup/           # Backups of replaced files
```

## License

This project is open source. Feel free to contribute or report issues.