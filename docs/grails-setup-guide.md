# Grails Development Environment Setup Guide

## Prerequisites
- Windows 10/11 or macOS
- Internet connection
- Administrator/sudo access

## Required Components
- SDKMAN (Software Development Kit Manager)
- Java 11
- Grails 6.2.3
- IntelliJ IDEA Community Edition

---

## Windows Setup Instructions

### Step 1: Install WSL2 (Windows Subsystem for Linux)

SDKMAN requires a Unix-like environment, so we'll use WSL2.

1. Open PowerShell as Administrator
2. Run the following command:
   ```powershell
   wsl --install
   ```
3. **Restart your computer** when prompted
4. After restart, open Ubuntu from the Start menu
5. Set up your Unix username and password when prompted

### Step 2: Install SDKMAN

1. Open WSL2 Ubuntu terminal
2. Install SDKMAN by running:
   ```bash
   curl -s "https://get.sdkman.io" | bash
   ```
3. Open a new terminal window or activate SDKMAN:
   ```bash
   source "$HOME/.sdkman/bin/sdkman-init.sh"
   ```
4. Verify installation:
   ```bash
   sdk version
   ```

### Step 3: Install Java 11 and Grails 6.2.3

In your WSL2 terminal:

```bash
# List available Java versions
sdk list java

# Install Java 11 (Temurin)
sdk install java 11.0.24-tem

# Install Grails 6.2.3
sdk install grails 6.2.3

# Set as default versions
sdk default java 11.0.24-tem
sdk default grails 6.2.3

# Verify installations
java -version
grails -version
```

### Step 4: Install IntelliJ IDEA Community Edition

1. Download from: https://www.jetbrains.com/idea/download/
2. Select **Windows** and download the Community Edition
3. Run the installer (.exe file)
4. During installation:
   - Check "Add launchers dir to the PATH"
   - Check "Add 'Open Folder as Project'"
5. Complete the installation

### Step 5: Configure IntelliJ IDEA for Grails

#### Install Grails Plugin (Required)

1. Launch IntelliJ IDEA
2. On the welcome screen, click **Plugins** (or go to File → Settings → Plugins)
3. Click on **Marketplace** tab
4. Search for "Grails" in the marketplace
5. Find the official **Grails** plugin and click **Install**
6. Restart IntelliJ IDEA when prompted

**Note**: The Grails plugin is essential for Grails development in IntelliJ IDEA and must be installed from the Plugin Marketplace.

#### Configure WSL2 JDK in IntelliJ:

1. Go to **File → Project Structure → SDKs**
2. Click the **+** button → **Add JDK**
3. Navigate to WSL path:
   ```
   \\wsl$\Ubuntu\home\[your-username]\.sdkman\candidates\java\11.0.24-tem
   ```
4. Click OK to add the JDK

#### Create a Grails Project:

1. **File → New → Project**
2. Select **Grails** from the left panel
3. Choose your WSL2 JDK
4. Set Grails home path:
   ```
   \\wsl$\Ubuntu\home\[your-username]\.sdkman\candidates\grails\6.2.3
   ```
5. Click Next and follow the project creation wizard

---

## macOS Setup Instructions

### Step 1: Install SDKMAN

1. Open Terminal (found in Applications → Utilities)
2. Install SDKMAN:
   ```bash
   curl -s "https://get.sdkman.io" | bash
   ```
3. Open a new terminal window or activate SDKMAN:
   ```bash
   source "$HOME/.sdkman/bin/sdkman-init.sh"
   ```
4. Verify installation:
   ```bash
   sdk version
   ```

### Step 2: Install Java 11 and Grails 6.2.3

In Terminal:

```bash
# List available Java versions
sdk list java

# Install Java 11 (Temurin)
sdk install java 11.0.24-tem

# Install Grails 6.2.3
sdk install grails 6.2.3

# Set as default versions
sdk default java 11.0.24-tem
sdk default grails 6.2.3

# Verify installations
java -version
grails -version
```

### Step 3: Install IntelliJ IDEA Community Edition

1. Download from: https://www.jetbrains.com/idea/download/
2. Select **macOS** and download the Community Edition
3. Open the downloaded .dmg file
4. Drag IntelliJ IDEA to the Applications folder
5. Open IntelliJ IDEA from Applications

### Step 4: Configure IntelliJ IDEA for Grails

#### Install Grails Plugin (Required)

1. Launch IntelliJ IDEA
2. On the welcome screen, click **Plugins** (or go to IntelliJ IDEA → Preferences → Plugins)
3. Click on **Marketplace** tab
4. Search for "Grails" in the marketplace
5. Find the official **Grails** plugin and click **Install**
6. Restart IntelliJ IDEA when prompted

**Note**: The Grails plugin is essential for Grails development in IntelliJ IDEA and must be installed from the Plugin Marketplace.

#### Configure JDK in IntelliJ:

1. Go to **File → Project Structure → SDKs**
2. Click the **+** button → **Add JDK**
3. Navigate to:
   ```
   /Users/[your-username]/.sdkman/candidates/java/11.0.24-tem
   ```
4. Click OK to add the JDK

#### Create a Grails Project:

1. **File → New → Project**
2. Select **Grails** from the left panel
3. Choose your JDK
4. Set Grails home path:
   ```
   /Users/[your-username]/.sdkman/candidates/grails/6.2.3
   ```
5. Click Next and follow the project creation wizard

---

## Verify Your Installation

### Create a Test Application

Open Terminal (macOS) or WSL2 Ubuntu (Windows):

```bash
# Create a new Grails application
grails create-app workshop-app
cd workshop-app

# Run the application
grails run-app
```

You should see output ending with:
```
Grails application running at http://localhost:8080 in environment: development
```

### Test in Browser

1. Open your web browser
2. Navigate to: http://localhost:8080
3. You should see the Grails welcome page

### Stop the Application

Press `Ctrl+C` in the terminal to stop the application.

---

## Common Issues and Solutions

### Windows-Specific Issues

**Issue**: Commands not recognized in WSL2
- **Solution**: Open a new terminal after SDKMAN installation

**Issue**: IntelliJ can't find WSL2 paths
- **Solution**: Ensure WSL2 is running and use `\\wsl$\` prefix for paths

**Issue**: Permission denied errors
- **Solution**: Run commands without `sudo` in WSL2

### macOS-Specific Issues

**Issue**: SDKMAN commands not found
- **Solution**: Ensure your shell profile (.zshrc or .bash_profile) sources SDKMAN:
  ```bash
  echo 'source "$HOME/.sdkman/bin/sdkman-init.sh"' >> ~/.zshrc
  source ~/.zshrc
  ```

**Issue**: IntelliJ can't execute Grails commands
- **Solution**: Configure IntelliJ terminal to use system shell:
  - Preferences → Tools → Terminal → Shell path: `/bin/zsh`

### General Issues

**Issue**: Port 8080 already in use
- **Solution**: Stop other applications or use a different port:
  ```bash
  grails run-app -port=8090
  ```

**Issue**: Grails plugin not available in IntelliJ
- **Solution**: Ensure you're using IntelliJ IDEA (not Android Studio), check the Marketplace tab in Plugins, and restart after plugin installation

---

## Additional Resources

- **Grails Documentation**: https://docs.grails.org/6.2.3/guide/single.html
- **SDKMAN Documentation**: https://sdkman.io/usage
- **IntelliJ IDEA Help**: https://www.jetbrains.com/help/idea/

---

## Workshop Tips

1. **Check Java Version**: Always verify you're using Java 11:
   ```bash
   java -version
   ```

2. **SDKMAN Commands Cheat Sheet**:
   - List installed versions: `sdk list grails`
   - Switch versions temporarily: `sdk use grails 6.2.3`
   - See current version: `sdk current grails`

3. **IntelliJ Shortcuts**:
   - Run application: `Shift+F10` (Windows) or `Ctrl+R` (Mac)
   - Open terminal: `Alt+F12` (Windows) or `Option+F12` (Mac)
   - Search everywhere: `Shift+Shift`

4. **Grails Commands**:
   - Create controller: `grails create-controller MyController`
   - Create domain class: `grails create-domain-class Person`
   - Generate all: `grails generate-all Person`

---

**Ready to start developing with Grails!**