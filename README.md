# auruby

![Language](https://img.shields.io/badge/language-Ruby-red.svg)
![License](https://img.shields.io/badge/license-MIT-blue.svg)

A simple, lightweight, and robust command-line AUR (Arch User Repository) helper written in pure Ruby with no dependencies outside of `httparty`.

`auruby` is designed to be a transparent and easy-to-understand tool for searching, installing, and managing AUR packages, with a focus on correct dependency resolution and configuration handling.

## Features

-   **Search and Install:** Search for packages on the AUR and install them with an interactive selection menu.
-   **Upgrade All AUR Packages:** Check for updates for all installed AUR packages and install them.
-   **Multi-Package Operations:** Install multiple packages in a single command (`auruby pkg1 pkg2`).
-   **Recursive Dependency Resolution:** Automatically detects and installs AUR dependencies before building the main package.
-   **Clean YAML Configuration:** Uses a simple, safe, and easy-to-read YAML file for configuration.
-   **Parallel Builds:** Configurable number of CPU cores to use for faster package compilation.
-   **Standard Flags:** Supports common flags like `--force` for re-cloning/rebuilding and `--yes` for non-interactive installation.

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
    chmod +x auruby
    ```

4.  **Place it in Your PATH**
    The best way to make `auruby` available everywhere is to create a symbolic link to it in `/usr/local/bin`.
    ```bash
    sudo ln -s "$(pwd)/auruby" /usr/local/bin/auruby
    ```
    Now you can run the `auruby` command from any directory.

## Configuration

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

# The number of CPU cores to use for `make`. (`nproc` is a good default).
number_cores: 4
```

**Note on Build Options:** `auruby` now respects the standard Arch Linux configuration files for `makepkg`. To customize build options (like disabling debug packages), please edit your global `/etc/makepkg.conf` file.

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

The `--force` flag will delete the cached source directory for a package before cloning and building it again. This is useful if a build directory gets corrupted.

```bash
auruby package-name --force
```

#### Non-interactive Installation

The `--yes` (or `--noconfirm`) flag will automatically answer "yes" to all prompts, including the final build and install confirmation.

```bash
auruby package-name --yes
```

#### Clean no longer wanted cache

The `--clean-cache` flag can be used to clean cached packages which are no longer installed or failed to build. 

```bash
auruby --clean-cache
```

## License

This project is licensed under the MIT License. See the `LICENSE` file for details.
