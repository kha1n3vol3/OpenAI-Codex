# OpenAI-Codex
![](codex.png)
> Codex CLI is an experimental project under active development. It is not yet stable, may contain bugs, incomplete features, or undergo breaking changes. We're building it in the open with the community and welcome:

This is an opionated install and setup for Codex from OpenAI: https://github.com/openai/codex feel free to choose your own adventure.

See this getting started guide: https://help.openai.com/en/articles/11096431-openai-codex-cli-getting-started

---

# PNPM Global Installation Setup

This guide provides instructions to configure `pnpm` for global package installations, especially in an environment using `nvm` (Node Version Manager). It includes steps to resolve common errors like `ERR_PNPM_NO_GLOBAL_BIN_DIR` and an installation script to automate the process. The example focuses on installing the `@openai/codex` package globally, though caution is advised regarding its legitimacy.

## Prerequisites

- **Node.js** installed via `nvm` (Node Version Manager). Verify with:
  ```bash
  nvm current
  node -v
  ```
- **pnpm** installed. If not, install it with:
  ```bash
  npm install -g pnpm
  ```

## Manual Setup Steps

If you prefer to configure `pnpm` manually or encounter issues with the script, follow these steps:

1. **Create and Configure Global Bin Directory**:
   Create a directory for global binaries and set appropriate permissions:
   ```bash
   mkdir -p ~/.pnpm/bin
   chmod -R 755 ~/.pnpm/bin
   ```
   Set this directory as the global bin directory for `pnpm`:
   ```bash
   pnpm config set global-bin-dir ~/.pnpm/bin
   ```

2. **Update Shell PATH**:
   Add the global bin directory to your shell's `PATH` environment variable. Add the following line to your shell configuration file (e.g., `~/.zshrc` or `~/.bashrc`):
   ```bash
   export PATH="$HOME/.pnpm/bin:$PATH"
   ```
   Reload the shell configuration:
   ```bash
   source ~/.zshrc  # or ~/.bashrc, depending on your shell
   ```

3. **Verify Node.js and pnpm Environment**:
   Check the active Node.js and `pnpm` versions to ensure compatibility:
   ```bash
   nvm current
   node -v
   pnpm --version
   ```

4. **Configure pnpm Store Directory**:
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

5. **Install a Global Package**:
   Install the desired package globally (e.g., `@openai/codex`):
   ```bash
   pnpm install -g @openai/codex
   ```
   For debugging issues, use verbose logging:
   ```bash
   pnpm install -g @openai/codex --loglevel verbose
   ```

6. **List Global Packages**:
   Verify the installation by listing all globally installed packages:
   ```bash
   pnpm list -g
   ```

7. **Set Specific pnpm Version (Optional)**:
   If you need a specific version of `pnpm`, use `corepack` to set it:
   ```bash
   corepack use pnpm@10.12.1
   ```

## Installation Script

To automate the setup process, use the following script. Save it as `setup-pnpm-global.sh`, make it executable, and run it.

```bash
#!/bin/bash

# Script to configure pnpm for global installations and install @openai/codex

echo "Setting up pnpm global configuration..."

# Step 1: Create and configure global bin directory
mkdir -p ~/.pnpm/bin
chmod -R 755 ~/.pnpm/bin
pnpm config set global-bin-dir ~/.pnpm/bin
echo "Global bin directory set to ~/.pnpm/bin"

# Step 2: Add global bin directory to PATH if not already present
if ! grep -q "export PATH=\"\$HOME/.pnpm/bin:\$PATH\"" ~/.zshrc 2>/dev/null && ! grep -q "export PATH=\"\$HOME/.pnpm/bin:\$PATH\"" ~/.bashrc 2>/dev/null; then
  echo "export PATH=\"\$HOME/.pnpm/bin:\$PATH\"" >> ~/.zshrc 2>/dev/null || echo "export PATH=\"\$HOME/.pnpm/bin:\$PATH\"" >> ~/.bashrc
  echo "Added ~/.pnpm/bin to PATH in shell config"
else
  echo "PATH already includes ~/.pnpm/bin"
fi

# Reload shell configuration
if [ -f ~/.zshrc ]; then
  source ~/.zshrc
  echo "Reloaded ~/.zshrc"
elif [ -f ~/.bashrc ]; then
  source ~/.bashrc
  echo "Reloaded ~/.bashrc"
else
  echo "Could not reload shell config. Please manually run 'source ~/.zshrc' or 'source ~/.bashrc'"
fi

# Step 3: Verify environment
echo "Current Node.js version: $(node -v)"
echo "Current nvm version: $(nvm current)"
echo "Current pnpm version: $(pnpm --version)"

# Step 4: Configure store directory
if [ -z "$(pnpm config get store-dir)" ] || [ ! -d "$(pnpm config get store-dir)" ]; then
  mkdir -p ~/.pnpm-store
  chown -R $(whoami):$(id -gn) ~/.pnpm-store
  chmod -R 755 ~/.pnpm-store
  pnpm config set store-dir ~/.pnpm-store
  echo "Store directory set to ~/.pnpm-store"
else
  echo "Store directory already configured: $(pnpm config get store-dir)"
fi

# Step 5: Set specific pnpm version (optional)
corepack use pnpm@10.12.1
echo "Set pnpm version to 10.12.1"

# Step 6: Install @openai/codex globally
echo "Installing @openai/codex globally..."
pnpm install -g @openai/codex --loglevel verbose

# Step 7: List global packages
echo "Listing globally installed packages:"
pnpm list -g

echo "Setup complete! Check above for any errors."
```

### **Make the Script Executable and Run It**

```bash
chmod +x setup-pnpm-global.sh
./setup-pnpm-global.sh
```

## Important Notes

- **Environment Compatibility**: This setup assumes you're using `nvm` to manage Node.js versions. Global packages are tied to the active Node.js version. If you switch versions with `nvm`, you may need to reinstall global packages.

- **Shell Configuration**: The script attempts to detect whether you're using `zsh` or `bash` by checking for `~/.zshrc` or `~/.bashrc`. Adjust the script if you use a different shell.

## Troubleshooting

- **Error `ERR_PNPM_NO_GLOBAL_BIN_DIR`**: Ensure the global bin directory (`~/.pnpm/bin`) exists and is in your `PATH`. Verify with:
  ```bash
  pnpm config get global-bin-dir
  echo $PATH
  ```
- **Permission Issues**: If you encounter permission errors, fix ownership:
  ```bash
  chown -R $(whoami):$(id -gn) ~/.pnpm/bin ~/.pnpm-store
  ```
- **Verbose Logging**: For detailed error messages, use:
  ```bash
  pnpm install -g @openai/codex --loglevel verbose
  ```

## License

This guide and script are provided as-is for educational purposes. Use at your own risk.

---
