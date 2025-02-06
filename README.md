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
</details>



