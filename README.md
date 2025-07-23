# auruby

A simple, lightweight, and robust command-line AUR (Arch User Repository) helper written in pure Ruby with minimal dependencies.

`auruby` is designed to be a transparent and easy-to-understand tool for searching, installing, and managing AUR packages, with a focus on correct dependency resolution and following Arch Linux conventions.

## Features

-   **Search and Install:** Search for packages on the AUR and install them with an interactive selection menu.
-   **Upgrade All AUR Packages:** Check for updates for all installed AUR packages and install them.
-   **Multi-Package Operations:** Install multiple packages in a single command (`auruby pkg1 pkg2`).
-   **Recursive Dependency Resolution:** Automatically detects and installs AUR dependencies before building the main package.
-   **Clean YAML Configuration:** Uses a simple, safe, and easy-to-read YAML file for configuration.
-   **Follows Arch Conventions:** Respects system-wide `makepkg.conf` settings for build configuration.
-   **Standard Flags:** Supports common flags like `--force` for re-cloning/rebuilding and `--yes` for non-interactive installation.
-   **Cache Management:** Clean unused package cache directories to reclaim disk space.

## Prerequisites

Before using `auruby`, you need to have a few things installed on your Arch Linux system:

-   **Ruby:** The programming language the script is written in.
-   **Pacman and Base-devel:** The core package management tools, including `makepkg`. You can install the build tools with `sudo pacman -S --needed base-devel`.
-   **Git:** Used to clone AUR package repositories.
-   **httparty:** The only Ruby gem dependency, used for making requests to the AUR RPC.

## Installation

1.  **Clone the Repository**
    ```bash
    git clone <your-github-repo-url>
    cd auruby
    ```

2.  **Install the Gem Dependency**
    ```bash
    gem install httparty
    ```

3.  **Make the Script Executable**
    ```bash
    chmod +x auruby.rb
    ```

4.  **Place it in Your PATH**
    The best way to make `auruby` available everywhere is to create a symbolic link to it in `/usr/local/bin`.
    ```bash
    sudo ln -s "$(pwd)/auruby.rb" /usr/local/bin/auruby
    ```
    Now you can run the `auruby` command from any directory.

## Configuration

### Application Configuration

`auruby` is configured with a simple YAML file located at `~/.config/auruby/config.yml`.

First, create the directory:
```bash
mkdir -p ~/.config/auruby
```

Then, create the configuration file `~/.config/auruby/config.yml` with your preferred settings.

#### Example `config.yml`

```yaml
# The maximum number of search results to display in the interactive menu.
max_results: 15
```

### Build Configuration

`auruby` follows standard Arch Linux conventions and respects your system's `makepkg.conf` settings. To configure build options such as parallel compilation, compiler flags, or package options:

**System-wide configuration (affects all users):**
```bash
sudo nano /etc/makepkg.conf
```

**User-specific configuration (affects only your user):**
```bash
nano ~/.makepkg.conf
```

#### Common Build Settings

```bash
# Use all available CPU cores for compilation
MAKEFLAGS="-j$(nproc)"

# Or specify a specific number of cores
MAKEFLAGS="-j8"

# Compiler optimizations
CFLAGS="-march=native -O2 -pipe -fno-plt -fexceptions"
CXXFLAGS="$CFLAGS -Wp,-D_FORTIFY_SOURCE=2"

# Skip debug package creation (smaller packages, faster builds)
OPTIONS=(strip docs !libtool !staticlibs emptydirs zipman purge !debug !lto)
```

This approach ensures that all `makepkg` operations (not just `auruby`) use consistent build settings.

## Usage

#### Upgrading All AUR Packages

Checks all of your installed AUR packages for new versions and prompts you to install any available updates.

```bash
auruby --upgrade
```

#### Searching for and Installing a Package

If there is an exact match, it will proceed. If not, it will present an interactive menu of the most popular results.

```bash
auruby package-name
```

#### Installing Multiple Packages

`auruby` will resolve and install each package and its dependencies sequentially.

```bash
auruby package-one package-two
```

#### Forcing a Re-clone and Rebuild

The `--force` flag will delete the cached source directory for a package before cloning and building it again. This is useful if a build directory gets corrupted or you want to ensure a fresh build.

```bash
auruby package-name --force
```

#### Non-interactive Installation

The `--yes` (or `--noconfirm`) flag will automatically answer "yes" to all prompts, including the final build and install confirmation.

```bash
auruby package-name --yes
```

#### Download Only (No Build)

The `--download-only` flag will clone the AUR repository and resolve dependencies but skip the actual building and installation.

```bash
auruby package-name --download-only
```

#### Clean Unused Cache

The `--clean-cache` flag can be used to clean cached packages which are no longer installed. This helps reclaim disk space.

```bash
auruby --clean-cache
```

## Command Line Options

```
Usage: auruby [options] <package_name> [package_name2]...
   or: auruby --upgrade
   or: auruby --clean-cache

Options:
    -f, --force                Force re-downloading of cached packages
    -d, --download-only        Download source files only, do not build
    -y, --yes, --noconfirm     Assume 'yes' to all prompts
    -u, --upgrade              Check for and install updates for all AUR packages
    -c, --clean-cache          Remove cached sources for uninstalled packages
    -h, --help                 Show this help message
```

## How It Works

1. **Search:** Queries the AUR RPC API for matching packages
2. **Selection:** If multiple results, presents an interactive menu sorted by popularity
3. **Dependency Resolution:** Parses PKGBUILD files to identify dependencies
4. **Recursive Installation:** Installs AUR dependencies before building the target package
5. **Build and Install:** Uses `makepkg -si` to build and install packages
6. **Cache Management:** Maintains git repositories in `~/.cache/auruby/` for faster subsequent builds

## Troubleshooting

### Build Failures
If a package fails to build, check:
- Your `makepkg.conf` settings are appropriate for your system
- All dependencies are installed (both official repo and AUR dependencies)
- The PKGBUILD is compatible with your system

### Permission Issues
Ensure you have:
- Proper sudo access for package installation
- Write permissions to `~/.cache/auruby/`

### Network Issues
`auruby` includes robust error handling for network connectivity issues when accessing the AUR.

## License

This project is licensed under the MIT License. See the `LICENSE` file for details.
