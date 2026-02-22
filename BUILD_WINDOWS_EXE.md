# How to Create the Windows EXE for Campus Share

Follow these steps to build the Windows executable and (optionally) create an installer.

## Prerequisites

1. **Flutter**  
   - Install [Flutter](https://flutter.dev) and use the version required by the project.  
   - The project uses **FVM** with Flutter **3.35.6** (see `.fvmrc`).  
   - If you use FVM: run `fvm install` in the repo root, then use `fvm flutter` instead of `flutter` below.

2. **Rust**  
   - Install [Rust](https://www.rust-lang.org/tools/install) (required to build the native core).

3. **Visual Studio Build Tools (Windows)**  
   - Install [Visual Studio](https://visualstudio.microsoft.com/) or **Build Tools for Visual Studio** with the **Desktop development with C++** workload so that the Windows Flutter build can compile.

4. **Inno Setup (optional, for installer)**  
   - Only needed if you want an `.exe` installer.  
   - Download and install [Inno Setup](https://jrsoftware.org/isinfo.php).

---

## Option A: Build the app only (no installer)

This produces the runnable EXE and DLLs in the build folder.

1. Open a terminal (e.g. PowerShell or Command Prompt).

2. Go to the **app** directory:
   ```bash
   cd "c:\Users\Dell\Downloads\Nouveau dossier (4)\localsend\app"
   ```

3. Get dependencies:
   ```bash
   flutter pub get
   ```
   (If you use FVM: `fvm flutter pub get`)

4. Build for Windows:
   ```bash
   flutter build windows
   ```
   (With FVM: `fvm flutter build windows`)

5. The output will be in:
   ```text
   app\build\windows\x64\runner\Release\
   ```
   - **campus_share.exe** (or the name from your Windows runner config) is the main executable.  
   - Run it from that folder (it needs the `.dll` and `data\` folder next to it).

You can zip the whole `Release` folder to distribute a portable version.

---

## Option B: Build and create an EXE installer with Inno Setup

The project includes a script and an Inno Setup file that copy the build to `D:\inno` and then build an installer. You can adapt paths if you prefer.

### 1. Build the Windows app

From the **repo root** (the folder that contains `app` and `scripts`):

```bash
cd app
fvm flutter clean
fvm flutter pub get
fvm flutter build windows
```

(If you don’t use FVM, use `flutter` instead of `fvm flutter`.)

### 2. Prepare the Inno Setup input folder

The script expects the built files and an icon in **D:\inno**. Either run the provided script (which does this for you) or do it manually:

**Using the script (from repo root):**

```powershell
.\scripts\compile_windows_exe.ps1
```

This will:

- Clean and build the app from `app`
- Create `D:\inno` and copy `app\build\windows\x64\runner\Release\*` into it
- Copy `app\assets\packaging\logo.ico` to `D:\inno`
- Copy `scripts\windows\x64\*` into `D:\inno` (if that folder exists)
- Create `D:\inno-result`
- Run Inno Setup with `scripts\compile_windows_exe-inno.iss`

**If the script fails** (e.g. missing `scripts\windows\x64` or `assets\packaging\logo.ico`), do the steps manually:

1. Create `D:\inno`.
2. Copy everything from `app\build\windows\x64\runner\Release\` into `D:\inno`.
3. Copy `app\assets\packaging\logo.ico` to `D:\inno` (or use another icon and update the Inno script).
4. If you have an MSIX helper (e.g. from another build), place it in `D:\inno` and ensure its name matches `MyAppMsixHelper` in `scripts\compile_windows_exe-inno.iss` (currently `campus_share_msix_helper.msix`).

### 3. Run Inno Setup

1. Open **Inno Setup Compiler**.
2. Open the script:
   ```text
   c:\Users\Dell\Downloads\Nouveau dossier (4)\localsend\scripts\compile_windows_exe-inno.iss
   ```
3. Build → Compile (or press Ctrl+F9).

The installer will be created in **D:\inno-result** (as set in the script), e.g.:

- `campus_share_1.17.0.exe` (or similar, depending on version).

---

## Summary

| Goal                         | Command / action |
|-----------------------------|-------------------|
| Run the app in debug       | From `app`: `flutter run -d windows` |
| Build EXE only             | From `app`: `flutter build windows` → use `build\windows\x64\runner\Release\` |
| Build EXE + installer      | Run `scripts\compile_windows_exe.ps1` then compile `scripts\compile_windows_exe-inno.iss` with Inno Setup |

If you don’t have FVM, use `flutter` instead of `fvm flutter` everywhere. The app name and executable are set to **Campus Share** / **campus_share** in the project configuration.
