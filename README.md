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
    "start": "electron-vite preview", // start electron app to preview production build
    "dev": "electron-vite dev", // start dev server and electron app
    "prebuild": "electron-vite build" // build for production
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

## New project
```shell
npm create @quick-start/electron@latest
```



## Boilerplate
```shell
npx degit alex8088/electron-vite-boilerplate electron-app
cd electron-app

npm install
npm run dev
```





















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

## electron-vite

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


