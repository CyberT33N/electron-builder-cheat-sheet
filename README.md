# electron-builder-cheat-sheet

- https://www.npmjs.com/package/electron-builder
- https://www.electron.build/





# Uninstall

<details><summary>Click to expand..</summary>

## Silent

### Powershell
```
Start-Process "Uninstall test.exe" -ArgumentList "/S" -Wait
```



</details>



















<br><br>
________
________
<br><br>




# Customizing the NSIS Installer

<details><summary>Click to expand..</summary>

## Custom NSIS Scripts (`installer.nsh`)

When using `electron-builder` with the NSIS target (`"target": ["nsis"]` in `package.json`) for creating Windows installers (`.exe`), you can customize the installation process beyond the default behavior by providing a custom NSIS include script.

**Integration:**

1.  **Create the script file:** Typically named `installer.nsh`. While the standard location is a `build/` directory at the project root, in this specific project, it's located at `src/renderer/assets/build/installer.nsh`.
2.  **Link in `package.json`:** Tell `electron-builder` where to find this script using the `include` option within the `build.nsis` configuration:
    ```json
    "build": {
      // ... other options
      "nsis": {
        // ... other nsis options
        "include": "src/renderer/assets/build/installer.nsh" 
      }
    }
    ```

**Common Use Case: Running Code During Installation/Uninstallation**

NSIS scripts allow defining specific "macros" that run at different stages. Common ones used with `electron-builder` are:

*   `!macro customInstall`: Executes custom commands *after* the main application files have been installed.
*   `!macro customUninstall`: Executes custom commands *before* the uninstaller removes files.
*   `!macro preInit`: Executes *before* the installer UI shows up or installation begins (Use with caution, as it can interfere with standard installer behavior).

## Debugging NSIS Scripts (`installer.nsh`)

Standard JavaScript debugging tools (`console.log`, debuggers) **do not work** for NSIS scripts, as they run in the installer environment *before* your Electron app starts.

**Using `MessageBox`:**

The primary way to debug NSIS scripts and inspect variable values is by using the `MessageBox` command. It displays a pop-up window during the installation process.

**Syntax:**

```nsh
MessageBox <Type> "<Your Text Message>"
; Example showing a variable's value:
MessageBox MB_OK "The installation directory is: $INSTDIR"
```

*   `MB_OK` is a common type, showing a simple box with an "OK" button. Other types exist (e.g., `MB_YESNO`, `MB_ICONINFORMATION`).
*   Use NSIS variable syntax (`$VARIABLE` or `${VARIABLE}`) within the quoted string to display their runtime values.
*   Remember to use `\\` if you need to display a literal backslash within the message string itself.

**Example Debugging Snippet:**

```nsh
!macro customInstall
    # Check variable values before using them
    MessageBox MB_OK "INSTDIR = $INSTDIR"
    MessageBox MB_OK "APP_EXECUTABLE_FILENAME = ${APP_EXECUTABLE_FILENAME}"
    MessageBox MB_OK "Attempting to pin: $INSTDIR\\${APP_EXECUTABLE_FILENAME}" # Note \\ for display

    # The actual command
    ${StdUtils.InvokeShellVerb} $0 "$INSTDIR" "${APP_EXECUTABLE_FILENAME}" ${StdUtils.Const.ShellVerb.PinToTaskbar}

    MessageBox MB_OK "Pin command executed." # Confirm execution reached this point
!macroend
```

---

## Common NSIS Variables/Constants (Relevant for `electron-builder`)

These variables are typically available within your `installer.nsh` script when run via `electron-builder`.

| Variable/Constant             | Description                                                                                                | Example Value (Typical)                |
| :---------------------------- | :--------------------------------------------------------------------------------------------------------- | :------------------------------------- |
| `$INSTDIR`                    | The installation directory chosen by the user or set by the installer.                                     | `C:\Users\User\AppData\Local\Programs\MyApp` or `C:\Program Files\MyApp` |
| `${APP_EXECUTABLE_FILENAME}`  | The filename of the main application executable.                                                           | `MyApp.exe`                            |
| `${PRODUCT_FILENAME}`         | The product name, often sanitized for use in filenames (might be same as `APP_EXECUTABLE_FILENAME` without `.exe`). | `MyApp`                                |
| `${PRODUCT_NAME}`             | The application name as defined in `package.json` (`productName` or `name`).                               | `My Application`                       |
| `$PROGRAMFILES` / `$PROGRAMFILES64` | The path to the Program Files directory (use `$PROGRAMFILES64` explicitly for 64-bit).                     | `C:\Program Files`                     |
| `$APPDATA`                    | The current user's Application Data directory (`%APPDATA%`).                                               | `C:\Users\User\AppData\Roaming`        |
| `$LOCALAPPDATA`               | The current user's local, non-roaming Application Data directory (`%LOCALAPPDATA%`).                         | `C:\Users\User\AppData\Local`          |
| `$DESKTOP`                    | The path to the current user's Desktop directory.                                                          | `C:\Users\User\Desktop`                |
| `$SMPROGRAMS`                 | The path to the current user's Start Menu Programs directory.                                              | `C:\Users\User\AppData\Roaming\Microsoft\Windows\Start Menu\Programs` |
| `$STARTMENU`                  | The path to the current user's Start Menu directory.                                                       | `C:\Users\User\AppData\Roaming\Microsoft\Windows\Start Menu` |
| `$SYSDIR`                     | The Windows System directory (System32).                                                                   | `C:\Windows\System32`                  |
| `$WINDIR`                     | The Windows directory.                                                                                     | `C:\Windows`                           |
| `${INSTALL_REGISTRY_KEY}`     | The registry key used by the installer (defined by `electron-builder`, e.g., `Software\Microsoft\Windows\CurrentVersion\Uninstall\{appId}`). | `Software\...\...\{guid}`              |

*Note: Some paths might differ based on Windows version, language, and user configuration.*

</details>










<br><br>
________
________
<br><br>

# FAQ

<details><summary>Click to expand..</summary>

# Installation

## Can not close application error while installe
- Manchmal erhält man diesen **Error** mitten in der **Installation**. Da steht dann, dass die **Applikation** nicht geschlossen werden kann, und man kann auf **Wiederholen** klicken.

Option 1:
- Uninstall

Option 2:
- Wenn man dann auf **Wiederholen** klickt, sollte das das **Problem** lösen, und danach ist bei erneuter Installation das Problem weg. Schwer zu sagen, was die **Ursache** dahinter ist. Ein **Neustart** hilft tatsächlich auch nicht, aber auf **Wiederholen** klicken hilft.
    
</details>


