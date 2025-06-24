# OpenAI-Codex
![](codex.png)
> Codex CLI is an experimental project under active development. It is not yet stable, may contain bugs, incomplete features, or undergo breaking changes. We're building it in the open with the community and welcome feedback.
This is an opinionated install and setup for Codex from OpenAI: [https://github.com/openai/codex](https://github.com/openai/codex). Feel free to choose your own adventure.
See this getting started guide: [https://help.openai.com/en/articles/11096431-openai-codex-cli-getting-started](https://help.openai.com/en/articles/11096431-openai-codex-cli-getting-started)
---
# PNPM Global Installation Setup for OpenAI Codex CLI
This guide provides instructions to configure `pnpm` for global package installations in an environment using `nvm` (Node Version Manager). It includes steps to install the latest LTS version of Node.js, ensure compatibility with Codex CLI (which requires Node.js 22 or newer), and automate the process with an installation script. The focus is on installing the `@openai/codex` package globally.

## Prerequisites
- **Terminal Access** with `curl` or `wget` installed (for downloading `nvm`).
- A supported shell like `zsh` or `bash` (this guide uses `zsh` syntax; adjust for `bash` if needed).
- **Node.js** and `nvm` (Node Version Manager) will be installed or updated as part of this guide.
- **pnpm** will be installed if not already present.

## Manual Setup Steps
If you prefer to configure `pnpm` and Node.js manually or encounter issues with the script, follow these steps:

1. **Install or Update `nvm` (Node Version Manager)**:
   If `nvm` is not installed or outdated, run:
   ```bash
   curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
   source ~/.zshrc  # or ~/.bashrc, depending on your shell
   ```
   Verify `nvm` installation:
   ```bash
   nvm --version
   ```

2. **Install the Latest LTS Version of Node.js**:
   Install the latest LTS version of Node.js:
   ```bash
   nvm install --lts
   ```
   Use the latest LTS version:
   ```bash
   nvm use --lts
   ```

3. **Set the Default Node.js Version for Codex CLI Compatibility**:
   Check the current Node.js version:
   ```bash
   node -v
   ```
   Codex CLI requires Node.js 22 or newer. If the LTS version is 22 or higher (e.g., `v22.x.x`), set it as default. If your `nvm alias` shows an alias like `lts/jod` for the latest LTS, use:
   ```bash
   nvm alias default jod  # Replace 'jod' with the alias for the latest LTS from 'nvm alias'
   ```
   Alternatively, set the specific version:
   ```bash
   nvm alias default 22.16.0  # Replace with the installed LTS version if >= 22
   ```
   If the LTS version is below 22 (e.g., `v20.x.x`), install Node.js 22 explicitly and set it as default:
   ```bash
   nvm install 22
   nvm alias default 22
   nvm use 22
   ```

4. **Create and Configure Global Bin Directory for `pnpm`**:
   Create a directory for global binaries and set permissions:
   ```bash
   mkdir -p ~/.pnpm/bin
   chmod -R 755 ~/.pnpm/bin
   ```
   Set this as the global bin directory for `pnpm`:
   ```bash
   pnpm config set global-bin-dir ~/.pnpm/bin
   ```

5. **Update Shell PATH**:
   Add the global bin directory to your shell's `PATH`. Add this line to your shell configuration file (e.g., `~/.zshrc` or `~/.bashrc`):
   ```bash
   export PATH="$HOME/.pnpm/bin:$PATH"
   ```
   Reload the shell configuration:
   ```bash
   source ~/.zshrc  # or ~/.bashrc
   ```

6. **Verify Node.js and pnpm Environment**:
   Check the active Node.js and `pnpm` versions to ensure compatibility (Node.js should be >= 22 for Codex CLI):
   ```bash
   nvm current
   node -v
   pnpm --version
   ```

7. **Configure pnpm Store Directory**:
   If the `pnpm` store directory is not set or accessible, configure it:
   ```bash
   pnpm config get store-dir
   ```
   If unset or incorrect, create a directory and set permissions:
   ```bash
   mkdir -p ~/.pnpm-store
   chown -R $(whoami):$(id -gn) ~/.pnpm-store
   chmod -R 755 ~/.pnpm-store
   pnpm config set store-dir ~/.pnpm-store
   ```

8. **Install `@openai/codex` Globally**:
   Install the Codex CLI package globally:
   ```bash
   pnpm install -g @openai/codex
   ```
   For debugging issues, use verbose logging:
   ```bash
   pnpm install -g @openai/codex --loglevel verbose
   ```

9. **List Global Packages**:
   Verify the installation by listing globally installed packages:
   ```bash
   pnpm list -g
   ```

10. **Set Specific pnpm Version (Optional)**:
    If you need a specific version of `pnpm`, set it with `corepack`:
    ```bash
    corepack use pnpm@10.12.1
    ```

11. **Test Codex CLI**:
    Ensure Codex CLI runs without version errors (Node.js should be >= 22):
    ```bash
    codex --version
    ```
    If you encounter a version error, switch to Node.js 22:
    ```bash
    nvm use 22
    codex --version
    ```

## Installation Script
To automate the setup process, use the following updated script. It installs `nvm`, sets up the latest LTS Node.js version (or Node.js 22 if LTS is below 22), configures `pnpm`, and installs `@openai/codex` globally. Save it as `setup-codex.sh`, make it executable, and run it.

```bash
#!/bin/bash
# Script to configure Node.js with nvm, pnpm for global installations, and install @openai/codex
echo "Setting up Node.js, pnpm, and Codex CLI..."

# Step 1: Install or update nvm (Node Version Manager)
if ! command -v nvm &> /dev/null; then
  echo "Installing nvm..."
  curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
  source ~/.zshrc 2>/dev/null || source ~/.bashrc
else
  echo "nvm already installed: $(nvm --version)"
fi

# Step 2: Install the latest LTS Node.js version
echo "Installing the latest LTS Node.js version..."
nvm install --lts
nvm use --lts

# Step 3: Check Node.js version and ensure compatibility with Codex CLI (requires Node.js 22+)
NODE_VERSION=$(node -v | cut -d'v' -f2 | cut -d'.' -f1)
if [ "$NODE_VERSION" -lt 22 ]; then
  echo "Current LTS Node.js version ($(node -v)) is below 22. Installing Node.js 22 for Codex CLI..."
  nvm install 22
  nvm use 22
  nvm alias default 22
else
  echo "Current LTS Node.js version ($(node -v)) meets Codex CLI requirement (>=22)."
  # Set default to the latest LTS alias (e.g., 'jod') or version
  LTS_ALIAS=$(nvm alias | grep '^lts/' | tail -1 | awk '{print $1}' | cut -d'/' -f2)
  if [ -n "$LTS_ALIAS" ]; then
    nvm alias default "$LTS_ALIAS"
    echo "Set default Node.js to LTS alias: $LTS_ALIAS"
  else
    nvm alias default "$(node -v | cut -d'v' -f2)"
    echo "Set default Node.js to current version: $(node -v)"
  fi
fi

# Step 4: Verify Node.js environment
echo "Current Node.js version: $(node -v)"
echo "Current nvm version: $(nvm current)"

# Step 5: Install pnpm if not already installed
if ! command -v pnpm &> /dev/null; then
  echo "Installing pnpm..."
  npm install -g pnpm
else
  echo "pnpm already installed: $(pnpm --version)"
fi

# Step 6: Create and configure global bin directory for pnpm
mkdir -p ~/.pnpm/bin
chmod -R 755 ~/.pnpm/bin
pnpm config set global-bin-dir ~/.pnpm/bin
echo "Global bin directory set to ~/.pnpm/bin"

# Step 7: Add global bin directory to PATH if not already present
if ! grep -q "export PATH=\"\$HOME/.pnpm/bin:\$PATH\"" ~/.zshrc 2>/dev/null && ! grep -q "export PATH=\"\$HOME/.pnpm/bin:\$PATH\"" ~/.bashrc 2>/dev/null; then
  echo "export PATH=\"\$HOME/.pnpm/bin:\$PATH\"" >> ~/.zshrc 2>/dev/null || echo "export PATH=\"\$HOME/.pnpm/bin:\$PATH\"" >> ~/.bashrc
  echo "Added ~/.pnpm/bin to PATH in shell config"
else
  echo "PATH already includes ~/.pnpm/bin"
fi

# Step 8: Reload shell configuration
if [ -f ~/.zshrc ]; then
  source ~/.zshrc
  echo "Reloaded ~/.zshrc"
elif [ -f ~/.bashrc ]; then
  source ~/.bashrc
  echo "Reloaded ~/.bashrc"
else
  echo "Could not reload shell config. Please manually run 'source ~/.zshrc' or 'source ~/.bashrc'"
fi

# Step 9: Configure pnpm store directory
if [ -z "$(pnpm config get store-dir)" ] || [ ! -d "$(pnpm config get store-dir)" ]; then
  mkdir -p ~/.pnpm-store
  chown -R $(whoami):$(id -gn) ~/.pnpm-store
  chmod -R 755 ~/.pnpm-store
  pnpm config set store-dir ~/.pnpm-store
  echo "Store directory set to ~/.pnpm-store"
else
  echo "Store directory already configured: $(pnpm config get store-dir)"
fi

# Step 10: Set specific pnpm version (optional)
corepack use pnpm@10.12.1
echo "Set pnpm version to 10.12.1"
echo "Current pnpm version: $(pnpm --version)"

# Step 11: Install @openai/codex globally
echo "Installing @openai/codex globally..."
pnpm install -g @openai/codex --loglevel verbose

# Step 12: List global packages
echo "Listing globally installed packages:"
pnpm list -g

# Step 13: Test Codex CLI
echo "Testing Codex CLI..."
codex --version || echo "Error: Codex CLI failed to run. Ensure Node.js version is >= 22 with 'nvm use 22'."

echo "Setup complete! Check above for any errors."
```

### Make the Script Executable and Run It
```bash
chmod +x setup-codex.sh
./setup-codex.sh
```

## Important Notes
- **Node.js Version Compatibility**: Codex CLI requires Node.js 22 or newer. The script checks if the latest LTS meets this requirement; if not, it installs Node.js 22 and sets it as the default to ensure compatibility.
- **Environment Compatibility**: Global packages are tied to the active Node.js version. If you switch versions with `nvm`, you may need to reinstall global packages.
- **Shell Configuration**: The script detects whether you're using `zsh` or `bash` by checking for `~/.zshrc` or `~/.bashrc`. Adjust the script if you use a different shell.
- **LTS Alias Setting**: Due to inconsistencies with dynamically setting `nvm` aliases, the script attempts to set the default to the latest LTS alias (e.g., `jod`) or falls back to the version number. Manual adjustment may be needed if errors occur.

## Troubleshooting
- **Codex CLI Version Error**: If you see "Codex CLI requires Node.js version 22 or newer", switch to Node.js 22:
  ```bash
  nvm use 22
  codex --version
  ```
- **Error `ERR_PNPM_NO_GLOBAL_BIN_DIR`**: Ensure the global bin directory (`~/.pnpm/bin`) exists and is in your `PATH`. Verify with:
  ```bash
  pnpm config get global-bin-dir
  echo $PATH
  ```
- **Permission Issues**: If you encounter permission errors, fix ownership:
  ```bash
  chown -R $(whoami):$(id -gn) ~/.pnpm/bin ~/.pnpm-store
  ```
- **Verbose Logging for pnpm**: For detailed error messages during installation:
  ```bash
  pnpm install -g @openai/codex --loglevel verbose
  ```
- **nvm Alias Issues**: If setting the default alias fails, manually set it based on `nvm alias` output:
  ```bash
  nvm alias default jod  # Replace 'jod' with the latest LTS alias
  ```
  Or use the version number:
  ```bash
  nvm alias default 22.16.0  # Replace with installed version >= 22
  ```

## License
This guide and script are provided as-is for educational purposes. Use at your own risk.
---

### Key Updates Made:
1. **Node.js LTS Installation**: Added steps and script logic to install the latest LTS version of Node.js using `nvm install --lts`.
2. **Codex CLI Compatibility**: Ensured Node.js version meets or exceeds 22 by checking the installed LTS version and installing Node.js 22 if necessary, setting it as default.
3. **Alias Setting Workaround**: Incorporated a semi-automated approach to set the `default` alias to the latest LTS (e.g., `jod`) or version number, avoiding dynamic one-liners that failed due to `nvm` or shell quirks.
4. **Script Automation**: Updated the installation script (`setup-codex.sh`) to handle `nvm` installation, Node.js version management, `pnpm` setup, and Codex CLI installation in a single flow.
5. **Troubleshooting**: Added specific guidance for Codex version errors and `nvm` alias issues based on our workflow discussion.

This updated document should provide a robust setup process that installs the newest LTS Node.js version and ensures compatibility with Codex CLI. If you have further modifications or encounter issues, let me know!
