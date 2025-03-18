# electron-vite-cheat-sheet


# Install
- https://electron-vite.org/guide/

## Existing electron.js project

<details><summary>Click to expand..</summary>

```shell
npm i electron-vite -D
```

<br><br>

package.json
```javascript
{
  "main": "./out/main/index.js",
  "scripts": {
    "start": "electron-vite preview",
    "dev": "electron-vite dev --watch",
    "build": "electron-vite build",
    "preview": "electron-vite preview",
  }
}
```

electron.vite.config.js
```javascript
import { defineConfig } from 'electron-vite'
import path from 'path'
import { fileURLToPath } from 'url'

const __dirname = path.dirname(fileURLToPath(import.meta.url))

export default defineConfig({
    main: {
    // Eintritspunkt für den Hauptprozess
        entry: 'src/main/index.js',
        resolve: {
            alias: {
                '@': path.resolve(__dirname, './src')
            }
        }
    },
    preload: {
    // Eintritspunkt für Preload-Skripte
        entry: 'src/preload/index.js',
        resolve: {
            alias: {
                '@': path.resolve(__dirname, './src')
            }
        }
    },
    renderer: {
    // Eintritspunkt für den Renderer-Prozess
        entry: 'src/renderer/index.html',
        root: path.resolve(__dirname, 'src/renderer'),
        resolve: {
            alias: {
                '@': path.resolve(__dirname, './src')
            }
        },
        build: {
            rollupOptions: {
                input: {
                    index: path.resolve(__dirname, 'src/renderer/index.html')
                }
            }
        }
    }
}) 
```
</details>













<br><br>
<br><br>

## New project

<details><summary>Click to expand..</summary>

```shell
npm create @quick-start/electron@latest
```

</details>











<br><br>
<br><br>





## Boilerplate

<details><summary>Click to expand..</summary>

```shell
npx degit alex8088/electron-vite-boilerplate electron-app
cd electron-app

npm install
npm run dev
```

</details>





















<br><br>
<br><br>
___
<br><br>
<br><br>


# Config
- https://electron-vite.org/config/

<details><summary>Click to expand..</summary>

When running `electron-vite` from the command line, it will automatically try to resolve a config file named `electron.vite.config.js` inside the project root. The most basic config file looks like this:

```js
// electron.vite.config.js
export default {
  main: {
    // vite config options
  },
  preload: {
    // vite config options
  },
  renderer: {
    // vite config options
  }
}
```

You can also explicitly specify a config file to use with the `--config` CLI option (resolved relative to cwd):

```sh
electron-vite --config my-config.js
```

**TIP:**  
`electron-vite` supports config files suffixed with `.ts`, `.mjs`, `.cjs`, `.mts`, and `.cts`.

---

## Config Intellisense

Since `electron-vite` ships with TypeScript typings, you can leverage your IDE's intellisense with JSDoc type hints:

```js
/**
 * @type {import('electron-vite').UserConfig}
 */
const config = {
  // ...
}

export default config
```

Alternatively, you can use the `defineConfig` helper, which provides intellisense without the need for JSDoc annotations:

```js
import { defineConfig } from 'electron-vite'

export default defineConfig({
  main: {
    // ...
  },
  preload: {
    // ...
  },
  renderer: {
    // ...
  }
})
```

---

## Conditional Config

If the config needs to conditionally determine options based on the command (`dev/serve` or `build`) or the mode (`development` or `production`), it can export a function instead:

```js
import { defineConfig } from 'electron-vite'

export default defineConfig(({ command, mode }) => {
  if (command === 'serve') {
    return {
      // dev specific config
      main: {
        // ...
      },
      preload: {
        // ...
      },
      renderer: {
        // ...
      }
    }
  } else {
    // command === 'build'
    return {
      // build specific config
    }
  }
})
```

You can also use the `defineViteConfig` helper:

```js
import { defineConfig, defineViteConfig } from 'electron-vite'

export default defineConfig({
  main: {
    // ...
  },
  preload: {
    // ...
  },
  renderer: defineViteConfig(({ command, mode }) => {
    if (command === 'dev') {
      return {
        // dev specific config
      }
    } else {
      return {
        // build specific config
      }
    }
  })
})
```

**TIP:**  
The `defineViteConfig` exports from Vite.

---

## Environment Variables

`electron-vite` doesn’t load `.env` files by default as the files to load can only be determined after evaluating the `electron-vite` config. However, you can use the exported `loadEnv` helper to load the specific `.env` file if needed:

```js
import { defineConfig, loadEnv } from 'electron-vite'

export default defineConfig(({ command, mode }) => {
  // Load env file based on `mode` in the current working directory.
  // By default, only env variables prefixed with `MAIN_VITE_`,
  // `PRELOAD_VITE_` and `RENDERER_VITE_` are loaded,
  // unless the third parameter `prefixes` is changed.
  const env = loadEnv(mode)
  return {
    // electron-vite config
  }
})
```

---

## Built-in Config

### Built-in Config for Main

| Option                                | Default                                                       |
|---------------------------------------|---------------------------------------------------------------|
| `build.target`                        | `node*`, automatically match Node compatible target for Electron (e.g., Electron 20 is node16.15) |
| `build.outDir`                        | `out\main` (relative to project root)                         |
| `build.lib.entry`                     | `src\main\{index|main}.{js|ts|mjs|cjs}`, empty string if not found |
| `build.lib.formats`                   | `cjs` or `es`, `es` is only supported on Electron 28+          |
| `build.reportCompressedSize`          | `false`, disable gzip-compressed size reporting, increase build performance |
| `build.rollupOptions.external`        | `electron` and all node built-in modules                      |
| `build.assetDir`                      | `chunks`                                                      |
| `build.minify`                        | `false`                                                       |
| `build.copyPublicDir`                 | `false`, always                                                |
| `build.ssr`                           | `true`, always                                                 |
| `build.ssrEmitAssets`                 | `true`, always                                                 |
| `ssr.noExternal`                      | `true`, always                                                 |
| `resolve.browserField`                | `false`, disable resolving to browser field                    |
| `resolve.mainFields`                  | `['module', 'jsnext:main', 'jsnext']`                          |
| `resolve.conditions`                  | `['node']`, first resolve require exports                      |
| `publicDir`                           | `resources`                                                   |
| `envPrefix`                           | `MAIN_VITE_` and `VITE_`                                       |

---

### Built-in Config for Preload

| Option                                | Default                                                       |
|---------------------------------------|---------------------------------------------------------------|
| `build.target`                        | `node*`, automatically match Node compatible target for Electron (e.g., Electron 20 is node16.15) |
| `build.outDir`                        | `out\preload` (relative to project root)                      |
| `build.lib.entry`                     | `src\preload\{index|preload}.{js|ts|mjs|cjs}`, empty string if not found |
| `build.lib.formats`                   | `cjs` or `es`, `es` is only supported on Electron 28+          |
| `build.reportCompressedSize`          | `false`, disable gzip-compressed size reporting, increase build performance |
| `build.rollupOptions.external`        | `electron` and all node built-in modules                      |
| `build.assetDir`                      | `chunks`                                                      |
| `build.minify`                        | `false`                                                       |
| `build.copyPublicDir`                 | `false`, always                                                |
| `build.ssr`                           | `true`, always                                                 |
| `build.ssrEmitAssets`                 | `true`, always                                                 |
| `ssr.noExternal`                      | `true`, always                                                 |
| `publicDir`                           | `resources`                                                   |
| `envPrefix`                           | `PRELOAD_VITE_` and `VITE_`                                    |

---

### Built-in Config for Renderer

| Option                                | Default                                                       |
|---------------------------------------|---------------------------------------------------------------|
| `root`                                | `src\renderer`                                                |
| `build.target`                        | `chrome*`, automatically match Chrome compatible target for Electron (e.g., Electron 20 is chrome104) |
| `build.outDir`                        | `out\renderer` (relative to project root)                     |
| `build.rollupOptions.input`           | `\src\renderer\index.html`, empty string if not found         |
| `build.modulePreload.polyfill`        | `false`, there is no need to polyfill Module Preload for the Electron renderers |
| `build.reportCompressedSize`          | `false`, disable gzip-compressed size reporting, increase build performance |
| `build.minify`                        | `false`                                                       |
| `envPrefix`                           | `RENDERER_VITE_` and `VITE_`                                   |


</details>



























<br><br>
<br><br>
___
<br><br>
<br><br>






# Command Line Interface


<details><summary>Click to expand..</summary>


### Aliases:
- `electron-vite dev`
- `electron-vite serve`

The command will build the main process and preload scripts source code, start a dev server for the renderer, and finally start the Electron app.

### `electron-vite preview`
The command will build the main process, preload scripts, and renderer source code, and start the Electron app to preview.

### `electron-vite build`
The command will build the main process, preload scripts, and renderer source code. This is usually run before packaging the Electron application.

---

## Options

### Universal Options

| Option                     | Description                                                 |
|----------------------------|-------------------------------------------------------------|
| `-c, --config <file>`       | Use specified config file                                   |
| `-l, --logLevel <level>`    | Set log level (optional: info, warn, error, silent)         |
| `-m, --mode <mode>`         | Set env mode                                                |
| `-w, --watch`               | Watch mode for hot reloading (default: false)               |
| `--ignoreConfigWarning`     | Ignore config warning (default: false)                      |
| `--sourcemap`               | Output source maps for debug (default: false)               |
| `--outDir <dir>`            | Output directory (default: out)                             |
| `--entry <file>`            | Specify electron entry file                                 |
| `-v, --version`             | Display version number                                      |
| `-h, --help`                | Display available CLI options                               |

**NOTE:**  
The `--ignoreConfigWarning` option allows you to ignore warnings when the config is missing (e.g., not using preload scripts).

---

### Dev Options

| Option                      | Description                                                 |
|-----------------------------|-------------------------------------------------------------|
| `--inspect [port]`           | Enable V8 inspector on the specified port (default: 5858)   |
| `--inspectBrk [port]`       | Like `--inspect` but pauses execution on the first line of JavaScript |
| `--remoteDebuggingPort`      | Port for remote debugging                                   |
| `--noSandbox`                | Forces renderer process to run un-sandboxed                 |
| `--rendererOnly`             | Only dev server for the renderer                            |

**NOTE:**  
- The `--inspect` option enables the V8 inspector on the specified port. An external debugger can connect to this port.  
- The `--inspectBrk` option pauses execution on the first line of JavaScript.  
- The `--remoteDebuggingPort` option is used for debugging with IDEs.  
- The `--noSandbox` option will force Electron to run without sandboxing, typically used for running Electron as root on Linux.  
- The `--rendererOnly` option skips the main process and preload scripts builds and starts a dev server for the renderer only. This significantly speeds up the dev command.

**WARNING:**  
When using the `--rendererOnly` option, the `electron-vite` command must be run at least once without changing the main process and preload scripts source code.

---

### Preview Options

| Option                      | Description                                                 |
|-----------------------------|-------------------------------------------------------------|
| `--noSandbox`                | Forces renderer process to run un-sandboxed                 |
| `--skipBuild`                | Skip build                                                  |

**NOTE:**  
- The `--noSandbox` option forces Electron to run without sandboxing. It's used when running Electron as root on Linux.  
- The `--skipBuild` option skips the build step and starts the Electron app to preview.








<br><br>
<br><br>


# Passing Arguments to Electron App
- https://electron-vite.org/guide/dev#passing-arguments-to-electron-app

<details><summary>Click to expand..</summary>


You can append a `--` after the `electron-vite` CLI with the arguments to be passed.

### Passing Electron CLI flags:
```json
"scripts": {
  "dev": "electron-vite dev -- --trace-warnings"
}
```

> **NOTE:**  
> `electron-vite` already supports `--inspect`, `--inspect-brk`, `--remote-debugging-port`, and `--no-sandbox` commands, so you don't need to do this for these commands. See [Command Line Interface](https://electron-vite.org/guide/dev#passing-arguments-to-electron-app) for more details.

### Passing application arguments:
```json
"scripts": {
  "dev": "electron-vite dev -- p1 p2"
}
```
All arguments after the double-dash (`--`) will be passed to the Electron application, and you can use `process.argv` to handle them.



</details>





</details>




























<br><br>
<br><br>
___
<br><br>
<br><br>


# Development

<details><summary>Click to expand..</summary>

# Project Structure 
- It is recommended to use the following project structure:
```
.
├──src
│  ├──main
│  │  ├──index.ts
│  │  └──...
│  ├──preload
│  │  ├──index.ts
│  │  └──...
│  └──renderer    # with vue, react, etc.
│     ├──src
│     ├──index.html
│     └──...
├──electron.vite.config.ts
├──package.json
└──...
```








<br><br>
<br><br>


### Detect debug mode:


# Option 1 (is.dev)
```typescript
  // HMR for renderer base on electron-vite cli.
  // Load the remote URL for development or the local html file for production.
  if (is.dev && process.env['ELECTRON_RENDERER_URL']) {
    mainWindow.loadURL(process.env['ELECTRON_RENDERER_URL'])
  } else {
    mainWindow.loadFile(join(__dirname, '../renderer/index.html'))
  }
```





<br><br>
<br><br>

# Option 2 (process.env.NODE_ENV):
- electron-vite will automatically set `NODE_ENV` with `development` or `production`. They also set `NODE_ENV_ELECTRON_VITE` with the same values
- https://electron-vite.org/guide/env-and-mode#modes
  - By default, dev command runs in development mode, the build and preview command runs in production mode. You can overwrite the default mode used for a command by passing the --mode option flag. See Vite Modes.

/main/index.ts:
```
const currentFilePath = fileURLToPath(import.meta.url)
const currentDirPath = path.dirname(currentFilePath)
const isDev = process.env.NODE_ENV === 'development';
const windowManager = new WindowManager(isDev, path.join(currentDirPath, '../preload/index.js'))
```

WindowManager():
```typescript
import { BrowserWindow, ipcMain, screen, app } from 'electron';
import Store from 'electron-store';
import path from 'path';

interface WindowBounds {
    width: number;
    height: number;
    x?: number;
    y?: number;
}

interface StoreSchema {
    windowBounds: WindowBounds;
}

type WindowControlAction = 'minimize' | 'maximize' | 'close';

type WindowEventName = 'resize' | 'move';

/**
 * Verwaltet das Hauptfenster der Anwendung
 */
export class WindowManager {
    private mainWindow: BrowserWindow | null;
    private readonly isDev: boolean;
    private readonly preloadPath: string;
    private readonly store: Store<StoreSchema>;

    /**
     * Erstellt einen neuen WindowManager
     * @param {boolean} isDev - Gibt an, ob die Anwendung im Entwicklungsmodus läuft
     * @param {string} preloadPath - Pfad zur Preload-Datei
     */
    constructor(isDev: boolean, preloadPath: string) {
        this.mainWindow = null;
        this.isDev = isDev;
        this.preloadPath = preloadPath;
        this.store = new Store<StoreSchema>();
    }

    /**
     * Erstellt das Hauptfenster der Anwendung
     * @returns {BrowserWindow} Das erstellte Hauptfenster
     */
    createWindow(): BrowserWindow {
        const primaryDisplay = screen.getPrimaryDisplay();
        const { width, height } = primaryDisplay.workAreaSize;

        // Lade die gespeicherte Fensterposition
        const windowBounds = this.store.get('windowBounds', {
            width: width,
            height: height,
            x: undefined,
            y: undefined
        });

        console.log('Creating window with preload path:', this.preloadPath);

        this.mainWindow = new BrowserWindow({
            width: windowBounds.width,
            height: windowBounds.height,
            x: windowBounds.x,
            y: windowBounds.y,
            frame: false,
            webPreferences: {
                nodeIntegration: false,
                contextIsolation: true,
                preload: this.preloadPath
            }
        });

        // Load the app
        if (this.isDev) {
            this.mainWindow.loadURL(process.env['ELECTRON_RENDERER_URL'] || '');
            this.mainWindow.webContents.openDevTools();
        } else {
            this.mainWindow.loadFile(path.join(app.getAppPath(), 'out/renderer/index.html'));
        }

        // Speichere die Fensterposition bei Änderungen
        this.mainWindow.on('resize', () => {
            const bounds = this.mainWindow?.getBounds();
            if (bounds) {
                this.store.set('windowBounds', bounds);
            }
        });

        this.mainWindow.on('move', () => {
            const bounds = this.mainWindow?.getBounds();
            if (bounds) {
                this.store.set('windowBounds', bounds);
            }
        });

        this.setupWindowControls();
        this.setupTabNavigation();
        this.setupCloseHandler();

        return this.mainWindow;
    }

    /**
     * Richtet die Fenstersteuerung ein
     * @private
     */
    private setupWindowControls(): void {
        ipcMain.on('window-control', (_, action: WindowControlAction) => {
            if (!this.mainWindow) return;

            switch (action) {
                case 'minimize':
                    this.mainWindow.minimize();
                    break;
                case 'maximize':
                    if (this.mainWindow.isMaximized()) {
                        this.mainWindow.unmaximize();
                    } else {
                        this.mainWindow.maximize();
                    }
                    break;
                case 'close':
                    this.mainWindow.close();
                    break;
            }
        });
    }

    /**
     * Richtet den Handler für das Schließen des Fensters ein
     * @private
     */
    private setupCloseHandler(): void {
        if (!this.mainWindow) return;

        this.mainWindow.on('closed', () => {
            this.mainWindow = null;
        });
    }

    /**
     * Gibt das aktuelle Hauptfenster zurück
     * @returns {BrowserWindow | null} Das aktuelle Hauptfenster oder null
     */
    getWindow(): BrowserWindow | null {
        return this.mainWindow;
    }
} 
```





























<br><br>
<br><br>


### `dependencies` vs. `devDependencies`

<details><summary>Click to expand..</summary>

#### **1. Was ist der Unterschied?**  
- **`dependencies`** → Alles, was die App **zur Laufzeit braucht** (z. B. Datenbank-Module, API-Clients).  
- **`devDependencies`** → Alles, was nur **zur Entwicklung nötig** ist (z. B. `electron-vite`, `eslint`, `webpack`).  

#### **2. Warum `externalizeDepsPlugin` nutzen?**  
Beim Bauen der App mit `electron-vite` gibt es zwei Möglichkeiten:  
1. **Externe Module nicht bundlen** (schneller & kleiner, aber muss vorhanden sein).  
2. **Alles bundlen** (größer, aber garantiert, dass alles funktioniert).  

💡 **Best Practice:**  
- **Main Process & Preload** → **Externe Module NICHT bundlen** (`externalizeDepsPlugin`).  
- **Renderer (UI)** → **Alles bundlen** (damit es überall läuft).  

#### **3. Was passiert beim Packen?**  
- **Alles in `dependencies` wird mitgeliefert.**  
- **`devDependencies` werden ignoriert.**  

#### **4. Ausnahme: ESM-Only-Module**  
Manche Module (z. B. `lowdb`, `node-fetch`) funktionieren nur als ESM. Die kann `electron-vite` nicht einfach weglassen, sondern muss sie in ein **CommonJS-Modul** umwandeln.  

Dafür:
```js
externalizeDepsPlugin({ exclude: ['lowdb'] })
```
Dann sorgt `rollupOptions` dafür, dass `lowdb` trotzdem sauber geladen wird.  

---

### **🚀 Fazit**  
1. **`dependencies`** für alles, was die App braucht, wenn sie läuft.  
2. **`devDependencies`** nur für Entwicklungstools.  
3. **`externalizeDepsPlugin`** hält den Main Process schlank, aber ESM-Module müssen manchmal explizit gebundled werden.  
4. **Renderer-UI wird immer vollständig gebundled**, um Probleme zu vermeiden.
   
</details>








</details>







































<br><br>
<br><br>
___
<br><br>
<br><br>


# HMR (Hot Module Replacement)

<details><summary>Click to expand..</summary>

Hier ist dein Cheat Sheet im Markdown-Format:  


## Create a Browser Window with HMR

In order to use HMR in the renderer, use environment variables to determine whether the window browser loads a local HTML file or a local URL.

### Example Code:

```js
function createWindow() {
  // Create the browser window
  const mainWindow = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      preload: path.join(__dirname, '../preload/index.js')
    }
  });

  // Load the local URL for development or the local HTML file for production
  if (!app.isPackaged && process.env['ELECTRON_RENDERER_URL']) {
    mainWindow.loadURL(process.env['ELECTRON_RENDERER_URL']);
  } else {
    mainWindow.loadFile(path.join(__dirname, '../renderer/index.html'));
  }
}
```

The variable `ELECTRON_RENDERER_URL` is the local URL where Vite is running.

## Multi-Page Handling

If there are multiple pages, use it like this:

```js
if (!app.isPackaged && process.env['ELECTRON_RENDERER_URL']) {
  mainWindow.loadURL(`${process.env['ELECTRON_RENDERER_URL']}/view.html`);
} else {
  mainWindow.loadFile(path.join(__dirname, '../renderer/view.html'));
}
```

## Notes

- In **development**, the renderer `index.html` file must reference your script code using:  
  ```html
  <script type="module">...</script>
  ```
- In **production**, the `BrowserWindow` should load the **compiled** `index.html` from the output directory, not the source code.


</details>






































<br><br>
<br><br>
___
<br><br>
<br><br>


# Hot Reloading 
- https://electron-vite.org/guide/hot-reloading
  
<details><summary>Click to expand..</summary>

Hier ist dein Cheat Sheet für **Hot Reloading** in Markdown-Format:  

## ⚠️ NOTE  
Hot reloading is available since **electron-vite 1.0.8**.

## What is Hot Reloading?  

Hot reloading allows for **quick rebuilding and restarting** of the Electron app when the **main process** or **preload scripts** change.  
Technically, it's not true hot reloading, but it offers a **better development experience**.

## 🔥 How `electron-vite` Implements Hot Reloading  

1. **Enables Rollup watcher** to detect changes in the main process and preload scripts.  
2. **Rebuilds & restarts** the Electron app when main process modules change.  
3. **Rebuilds & reloads** renderers when preload script modules change.  

## 🚀 Enabling Hot Reloading  

You have two options:  

### 1️⃣ CLI Option (Recommended)  
Use `-w` or `--watch` flag when running the dev server:  
```sh
electron-vite dev --watch
```

### 2️⃣ Config Option  
Set `build.watch = {}` in the configuration file:  

```js
export default {
  build: {
    watch: {} // Enables watcher with default options
  }
}
```
For advanced settings, refer to `WatcherOptions`.

## ⚠️ NOTE  
Hot reloading **only works in development** mode.

## When to Use Hot Reloading?  

- Since **reload timing is unpredictable**, hot reloading **isn't always beneficial**.  
- It's best to **use the CLI option** to toggle it on/off **as needed**.  

</details>





















<br><br>
<br><br>
___
<br><br>
<br><br>






# 🛠️ Env Variables & Modes in electron-vite

<details><summary>Click to expand..</summary>

## 💡 TIPS  
🔹 **Empfohlen:** Lies zuerst [Vite's Env Variables & Modes Dokumentation](https://vitejs.dev/guide/env-and-mode.html).  

---

## 🌍 Global Env Variables  

electron-vite lädt Umgebungsvariablen aus dem Projekt-Root und verwendet **verschiedene Präfixe**, um die Reichweite zu begrenzen.  

### 🔑 Standard-Prefixe  
| Prefix             | Verfügbar in                 |
|--------------------|----------------------------|
| `MAIN_VITE_`      | Nur **Main Process**       |
| `PRELOAD_VITE_`   | Nur **Preload Scripts**    |
| `RENDERER_VITE_`  | Nur **Renderer**           |
| `VITE_`           | **Überall** verfügbar      |

### 🔍 Beispiel `.env` Datei  
```env
KEY=123                # ❌ Nicht verfügbar
MAIN_VITE_KEY=123      # ✅ Nur Main Process
PRELOAD_VITE_KEY=123   # ✅ Nur Preload Scripts
RENDERER_VITE_KEY=123  # ✅ Nur Renderer
VITE_KEY=123           # ✅ Überall verfügbar
```

---

## ⚙️ Custom Env Prefix  
👉 Falls du ein anderes Präfix benötigst, kannst du `envPrefix` setzen:  

```js
// electron.vite.config.js
export default defineConfig({
  main: {
    envPrefix: 'M_VITE_'
  }
  // ...
})
```

📌 Dadurch sind nur Variablen mit `M_VITE_` im Main Process verfügbar.

---

## 📝 TypeScript Unterstützung  
Falls du TypeScript nutzt, füge **Type-Definitionen** für `import.meta.env` in `env.d.ts` hinzu:  

```ts
/// <reference types="vite/client" />

interface ImportMetaEnv {
  readonly MAIN_VITE_SOME_KEY: string
  // Weitere Env Variablen...
}

interface ImportMeta {
  readonly env: ImportMetaEnv
}
```

🎯 **Vorteile**:  
✅ **Type Checking**  
✅ **Intellisense für Umgebungsvariablen**  

---

## 🚀 Modes in electron-vite  

### 🔄 Standard-Modi  
| Command               | Mode        |
|-----------------------|------------|
| `npm run dev`        | **development** |
| `npm run build`      | **production**  |
| `npm run preview`    | **production**  |

### 🏗️ Modus überschreiben  
Nutze `--mode`, um den Modus zu ändern:  
```sh
npm run dev --mode=staging
```
📌 Siehe [Vite Modes](https://vitejs.dev/guide/env-and-mode.html) für Details.







Example Usage:
```typescript
 try {
    window.electron?.send('execute-command', `rclone listremotes | grep '^${import.meta.env.VITE_RCLONE_PROTON_CONFIG_NAME}:$'`)
} catch (error) {
    console.error('Error while checking the configuration:', error)
    setIsLoading(false)
}
```





</details>






























<br><br>
<br><br>
___
<br><br>
<br><br>






# Debugging

<details><summary>Click to expand..</summary>

`electron-vite` supports debugging both the main process and the renderer process code.

## TIP

When you customize the build output directory via the `--outDir` CLI argument, the debugger configuration should also add this argument. But this is not needed when customizing via `build.outDir` of the configuration file.



## NPM Run script
- On Windows we must use cross-env on linux you can remove it
```javascript
    "start:debug-main": "cross-env REMOTE_DEBUGGING_PORT=9222 electron-vite dev --sourcemap",
    "start:debug-renderer": "node --inspect-brk=9222",
    "debug:all": "npm run start:debug-main & npm run start:debug-renderer",
```



<br><br>
<br><br>

## VSCode


### Launch File

Add a file `.vscode/launch.json` with the following configuration:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Debug Main Process",
      "type": "node",
      "request": "launch",
      "cwd": "${workspaceRoot}",
      "runtimeExecutable": "${workspaceRoot}/node_modules/.bin/electron-vite",
      "windows": {
        "runtimeExecutable": "${workspaceRoot}/node_modules/.bin/electron-vite.cmd"
      },
      "runtimeArgs": ["--sourcemap"],
      "env": {
        "REMOTE_DEBUGGING_PORT": "9222"
      }
    },
    {
      "name": "Debug Renderer Process",
      "port": 9222,
      "request": "attach",
      "type": "chrome",
      "webRoot": "${workspaceFolder}/src/renderer",
      "timeout": 60000,
      "presentation": {
        "hidden": true
      }
    }
  ],
  "compounds": [
    {
      "name": "Debug All",
      "configurations": ["Debug Main Process", "Debug Renderer Process"],
      "presentation": {
        "order": 1
      }
    }
  ]
}
```

Then set some breakpoints in the (main process or renderer process) source code. Go to the **Debug** view and ensure **Debug All** is selected. You can then press `F5` to start debugging.

## NOTE

You can also choose **Debug Main Process** to only debug the main process. Since renderer debugging can only be attached, it is not possible to debug the renderer process alone.
  
</details>





























<br><br>
<br><br>
___
<br><br>
<br><br>






# Source Code Protection in Electron

<details><summary>Click to expand..</summary>

## 📌 NOTE  
Source code protection is available since **electron-vite 1.0.9**.

### 🚨 Why Protect Your Code?  
Electron uses JavaScript, making it easy for attackers to:
- Unpack applications  
- Modify logic to bypass restrictions  
- Repackage and redistribute cracked versions  

## 🛠️ Solutions  
Besides moving commercial logic to the server, code hardening is essential:

1. **Uglify / Obfuscator** → Reduces readability of JS code  
2. **Native Encryption** → Encrypts bundle via XOR/AES inside a Node Addon  
3. **ASAR Encryption** → Encrypts the Electron ASAR file, modifies Electron source to decrypt before reading  
4. **V8 Bytecode** → Uses Node's `vm` module to generate V8 bytecode  

### 🆚 Comparison Table  

| Protection Method  | Unpack | Tampering | Readability | Repackaging | Access Cost | Overall Protection |
|--------------------|--------|-----------|-------------|-------------|-------------|---------------------|
| **Obfuscator**     | Easy   | Easy      | Easy        | Easy        | Low         | Low                 |
| **Native Encryption** | High  | Easy      | Easy        | Easy        | High        | Middle              |
| **ASAR Encryption** | High  | Middle    | Easy        | Easy        | High        | Middle              |
| **V8 Bytecode**    | High   | High      | High        | Easy        | Middle      | High                |

👉 **V8 Bytecode is currently the most effective solution.**

---

## 🎯 What is V8 Bytecode?  
V8 Bytecode is a **compiled form of JavaScript** used by the V8 engine.  
✅ Protects source code  
✅ Improves performance  

### **electron-vite Implementation**  
- Parses bundles and determines if they should be compiled to bytecode  
- Uses Electron to compile `.jsc` files  
- Generates a loader for Electron to run bytecode modules  
- Supports selective chunk compilation  

---

## 🚀 Enable Bytecode Protection  
Use the `bytecodePlugin` plugin in **electron-vite**:

```js
import { defineConfig, bytecodePlugin } from 'electron-vite'

export default defineConfig({
  main: { plugins: [bytecodePlugin()] },
  preload: { plugins: [bytecodePlugin()] },
  renderer: { /* ... */ }
})
```

⚠️ **Important Notes:**  
- `bytecodePlugin` **only works in production**.  
- The **preload script must disable the sandbox** (`sandbox: false`).  

---

## 🔧 bytecodePlugin Options  

### `chunkAlias`  
- **Type:** `string | string[]`  
- Instructs which chunks should be compiled to bytecode.  

### `transformArrowFunctions`  
- **Type:** `boolean` (default: `true`)  
- Converts arrow functions to normal functions.  

### `removeBundleJS`  
- **Type:** `boolean` (default: `true`)  
- If `false`, retains original `.js` files along with bytecode.  

### `protectedStrings`  
- **Type:** `string[]`  
- Protects sensitive strings (e.g., encryption keys) using `String.fromCharCode()`.  

Example:  
```js
import { defineConfig, bytecodePlugin } from 'electron-vite'

export default defineConfig({
  main: { plugins: [bytecodePlugin({ protectedStrings: ['ABC'] })] }
})
```

⚠️ **Minification (`build.minify`) must be disabled** when protecting strings!  

---

## 🏗️ Multi-Platform & Architecture Builds  

### ✅ Multi-Platform on Same Architecture  
Example:  
- **64-bit** Electron app for **MacOS, Windows, Linux** from a **64-bit MacOS** machine.  

### ⚠️ Multi-Architecture on Same Platform  
If building for **x64 on arm64 MacOS**, set `ELECTRON_EXEC_PATH`:

```js
import { defineConfig } from 'electron-vite'

export default defineConfig(() => {
  process.env.ELECTRON_EXEC_PATH = '/path/to/electron-x64/electron.app'

  return {
    // electron-vite config
  }
})
```

You can install Electron for another architecture using:  
```sh
npm install --arch=ia32 electron
```

🚨 **Bytecode is CPU-agnostic, but run tests to avoid rare CPU compatibility issues.**

---

## ❓ FAQ  

### 🔍 Does it affect code organization?  
- **Yes**: `Function.prototype.toString()` **won't work** (source code isn't distributed).  
- **No**: No impact on execution performance (minor improvement possible).  

### 📦 Impact on app size?  
- Small bundles (**< 1MB**) → Significant bytecode size increase.  
- Large bundles (**> 2MB**) → No noticeable size difference.  

### 🔐 How secure is it?  
- Currently **no known tools** exist to decompile V8 bytecode.  


</details>








































<br><br>
<br><br>
___
<br><br>
<br><br>






# Utils

<details><summary>Click to expand..</summary>


# electron-toolkit
- You can find all here:
  - https://github.com/alex8088/electron-toolkit?tab=readme-ov-file

## @electron-toolkit/
- https://www.npmjs.com/package/@electron-toolkit/utils

## @electron-toolkit/preload
- https://github.com/alex8088/electron-toolkit/tree/master/packages/preload


  
</details>























<br><br>
<br><br>
___
<br><br>
<br><br>


# FAQ

<details><summary>Click to expand..</summary>

# sandbox fix
```
sudo chown root:root node_modules/electron/dist/chrome-sandbox && sudo chmod 4755 node_modules/electron/dist/chrome-sandbox
```
- Related to (https://electron-vite.org/guide/cli#preview-options) `The --noSandbox option will force Electron run without sandboxing. It is commonly used to enable Electron to run as root on Linux.`
  
</details>











