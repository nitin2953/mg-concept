<div align="center">
  <img src="app-icon-ORIGINAL.png" alt="Logo" width="150" />
</div>

# **[MG Concept](https://mgconcept.in/courses) - Native Windows Client built using [Tauri v2](https://tauri.app/)**

A lightweight (~8MB), native Windows wrapper for the MG Concept Website built with Tauri v2 and Bun. Old offical app (Electron version) was not working as of June 2026, it had auto-closing issues & was using so much RAM, this new unofficial client consumes significantly less RAM (~5MB) by utilizing the native Windows Edge WebView2 engine.


### 📥 Download Pre-Compiled App
A ready-to-install Windows installer (.exe) is available directly in the [Releases](https://github.com/nitin2953/mg-concept/releases/latest) section. Please review the release notes for standard instructions on bypassing the Windows SmartScreen warning for unsigned applications. You can use this pre-built version if you prefer not to build the application from source.


### 🛡️ Security & Code Audit

This repository is 100% open-source and intentionally kept minimal. Because it is a native wrapper, the core logic is handled by standard JSON configurations rather than complex backend code. 

If you are unsure about the codebase or want to verify its safety, you are highly encouraged to audit the files. You can copy the code into any AI tool (like Gemini, ChatGPT or Claude) to review it. Alternatively, you can click the **GitHub Copilot** icon at the top of this repository page and ask the AI to directly scan the files for any security risks, malware, or malicious scripts.

---

## **🛠️ Prerequisites for building it yourself**

To build this application from source on a fresh Windows PC, you will need three standard development tools:

1. **Microsoft Visual Studio C++ Build Tools (2022)**
   - **Purpose:** Required to compile the C++ WebView2 rendering engine.
   - **Download:** [https://visualstudio.microsoft.com/visual-cpp-build-tools/](https://visualstudio.microsoft.com/visual-cpp-build-tools/)
   - **Setup:** Run the installer and check the box for **"Desktop development with C++"**.
   - **Size:** \~5 MB installer | \~2.5 GB storage footprint.
2. **Bun (Faster Node.js Alternative)**
   - **Purpose:** Acts as the package manager to download Tauri dependencies and run CLI scripts.
   - **Download:** Open PowerShell and run: `powershell -c "irm bun.sh/install.ps1 | iex"`
   - **Size:** \~50 MB | \~100 MB storage footprint.
3. **Rust (Rustup)**
   - **Purpose:** The core programming language that compiles the secure backend and .exe.
   - **Download:** [https://rustup.rs/](https://rustup.rs/)
   - **Setup:** Download `rustup-init.exe` and press `1` for the default installation.
   - **Size:** \~10 MB installer | \~1.5 GB storage footprint.

## **🚀 How to Build the App (From Scratch)**

You do not need to write or edit any Rust code (`lib.rs`) to build this app. Everything is controlled via a simple JSON configuration.

### **Step 1: Make a folder & Initialize the Project**

Open your terminal (cmd) and run the Tauri creation script using Bun:
this will also create a new folder automatically
```sh
cd C:/Users/XXXXX/Desktop

bunx create-tauri-app@latest
```
Follow the interactive prompts exactly like this:

1. **Project name:** `mg-concept`
2. **Identifier:** `com.mgconcept.app`
3. **Choose which language to use for your frontend:** `JavaScript / TypeScript`
4. **Choose your package manager:** `bun`
5. **Choose your UI template:** `Vanilla`

### **Step 2: Download Dependencies**

Navigate into your new project folder and download the required libraries. *(If you skip this, the build command will fail\!)*

```sh
cd mg-concept
bun install
```

### **Step 3: Clean up the Bloat**

Because this app strictly loads a live URL, the default starter files are completely unnecessary.

- Delete all files inside `src` folder from your project directory.
- This keeps the repository clean and prevents unused HTML/JS files from being packaged.

### **Step 4: Configure the Wrapper (tauri.conf.json)**

Open `src-tauri/tauri.conf.json` [(view file)](src-tauri/tauri.conf.json) and replace its contents with the configuration below.

```json
{
  "$schema": "https://schema.tauri.app/config/2",
  "productName": "MG Concept",
  "version": "1.0.0",
  "identifier": "com.mgconcept.app",
  "build": {
    "frontendDist": "https://mgconcept.in/courses"
  },
  "app": {
    "withGlobalTauri": true,
    "windows": [
      {
        "title": "MG Concept",
        "width": 1024,
        "height": 600,
        "decorations": true,
        "backgroundColor": "#fff",
        "center": true,
        "maximized": true,
        "url": "https://mgconcept.in/courses"
      }
    ],
    "security": {
      "csp": null
    }
  },
  "bundle": {
    "active": true,
    "targets": ["nsis"],
    "icon": [
      "icons/32x32.png",
      "icons/128x128.png",
      "icons/128x128@2x.png",
      "icons/icon.icns",
      "icons/icon.ico"
    ],
    "windows": {
      "nsis": {
        "installerIcon": "icons/icon.ico"
      }
    }
  }
}
```

**What this JSON does:**

- `width/height`: Sets the default starting size of the window.
- `center: true` : Ensures the app opens in the exact middle of the user's screen.
- `decorations: true` : Keeps the standard Windows title bar (with minimize, maximize, and close buttons) visible.
- `maximized: true` : The app will open filling the entire screen by default.
- `url` : Bypasses local files and natively loads the secure coaching website.
- —--
- `targets: ["nsis"]` : **Forces Tauri to ONLY build the lightweight NSIS setup installer**, If you want the MSI builder then set it to `"targets": "all",` .
- `icon` & `installerIcon` : Binds your custom extracted `.png` logos to both the executable and the NSIS setup wizard.

### **Step 5: Generate App Icons**

1. Place your high-quality logo image (e.g., `app-icon-ORIGINAL.png`) directly in the root `mg-concept` folder.
2. Run the automated icon generator:

```sh
bunx tauri icon app-icon-ORIGINAL.png
```

*Tauri will automatically generate all necessary `.ico`, `.icns`, and `.png` sizes and place them in the `src-tauri/icons` folder.*

### **Step 6: Compile the Installer**

Generate the final, production-ready Windows installer:

```sh
bunx tauri build
```

⏳ **IMPORTANT: Compilation Times**

- **First Build:** Because the compiler must build the native Rust and C++ WebView2 engines from scratch, the very first build will take approximately **10 to 12 minutes**. Do not close the terminal; let it process.
- **Subsequent Builds:** Once the initial cache is generated, any future builds or updates will be extremely fast, taking only **~1 to 2 minutes**.

Once the compilation finishes, your installer will be located at: `src-tauri/target/release/bundle/nsis/MG Concept_1.0.0_x64-setup.exe`
