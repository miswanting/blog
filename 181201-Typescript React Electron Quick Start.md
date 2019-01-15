目录结构：

```bash
mkdir test
cd test
mkdir src
mkdir src\components
```

安装依赖：

```npm init
npm init
npm i -D webpack webpack-cli
npm i --save react react-dom @types/react @types/react-dom
npm i -D typescript awesome-typescript-loader source-map-loader
npm i -D electron electron-builder electron-devtools-installer
```

修改文件：

入口：main.ts

```typescript
import { app, Menu, ipcMain, BrowserWindow } from 'electron'
import installExtension, { REACT_DEVELOPER_TOOLS } from 'electron-devtools-installer'
let win: BrowserWindow = null
app.on('ready', () => { // 启动程序
    createWindow()
})
function createWindow() {
    win = new BrowserWindow({ width: 1024, height: 768 })
    win.loadFile('src/index.html')
    win.webContents.openDevTools() // 生产环境下请注释掉
    // var menu = Menu.buildFromTemplate(menu_bar)
    Menu.setApplicationMenu(null)
    win.on('closed', () => {
        win = null
        console.log('[DEBG]检测到窗口关闭');
        app.quit()
    })
    // 加载 REACT DEVELOPER TOOLS（生产环境下请注释掉）
    installExtension(REACT_DEVELOPER_TOOLS)
        .then((name) => console.log(`[DEBG]添加插件：${name}`))
        .catch((err) => console.log('[DEBG]添加插件错误：', err))
}
```

tsconfig.json

```json
{
    "compilerOptions": {
        "outDir": "./dist/",
        "sourceMap": true,
        "noImplicitAny": false,
        "module": "commonjs",
        "target": "es5",
        "jsx": "react"
    },
    "include": [
        "./src/**/*"
    ]
}
```

webpack.dev.js

```javascript
const path = require('path');
const webpack = require('webpack')
const mainConfig = {
    mode: "development",
    target: 'electron-main',
    entry: "./src/main.ts",
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'main.js'
    },
    devtool: "source-map",
    resolve: {
        extensions: [".ts", ".js", ".json"]
    },
    module: {
        rules: [
            {
                test: /\.tsx?$/,
                use: "awesome-typescript-loader",
                // include: path.resolve(__dirname, 'src')
            },
            {
                enforce: "pre",
                test: /\.jsx?$/,
                loader: "source-map-loader"
            }
        ]
    },
};

const rendererConfig = {
    mode: "development",
    target: 'electron-renderer',
    entry: "./src/renderer.tsx",
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'renderer.js'
    },
    devtool: "source-map",
    resolve: {
        extensions: [".ts", ".tsx", ".js", ".jsx", ".json"]
    },
    module: {
        rules: [
            {
                test: /\.tsx?$/,
                use: "awesome-typescript-loader"
            },
            {
                enforce: "pre",
                test: /\.jsx?$/,
                use: "source-map-loader"
            },
            {
                test: /\.css$/,
                use: ['style-loader', 'css-loader']
            },
            {
                test: /\.(ttf|otf|eot|svg|woff(2)?)$/,
                use: 'file-loader'
            },
            {
                test: /\.(png|jpg|gif)$/,
                use: [
                    {
                        loader: 'file-loader',
                        options: {}
                    }
                ]
            }
        ]
    },
    plugins: [
        new webpack.ProvidePlugin({
            $: 'jquery',
            jQuery: 'jquery'
        })
    ]
};

module.exports = [mainConfig, rendererConfig];
```

webpack.prod.js

```javascript
const path = require('path');
const webpack = require('webpack')
const mainConfig = {
    mode: "production",
    target: 'electron-main',
    entry: "./src/main.ts",
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'main.js'
    },
    resolve: {
        extensions: [".ts", ".js", ".json"]
    },
    module: {
        rules: [
            {
                test: /\.tsx?$/,
                use: "awesome-typescript-loader",
                // include: path.resolve(__dirname, 'src')
            },
            {
                enforce: "pre",
                test: /\.jsx?$/,
                loader: "source-map-loader"
            }
        ]
    },
};

const rendererConfig = {
    mode: "production",
    target: 'electron-renderer',
    entry: "./src/renderer.tsx",
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'renderer.js'
    },
    resolve: {
        extensions: [".ts", ".tsx", ".js", ".jsx", ".json"]
    },
    module: {
        rules: [
            {
                test: /\.tsx?$/,
                use: "awesome-typescript-loader"
            },
            {
                enforce: "pre",
                test: /\.jsx?$/,
                use: "source-map-loader"
            },
            {
                test: /\.css$/,
                use: ['style-loader', 'css-loader']
            },
            {
                test: /\.(ttf|otf|eot|svg|woff(2)?)$/,
                use: 'file-loader'
            },
            {
                test: /\.(png|jpg|gif)$/,
                use: [
                    {
                        loader: 'file-loader',
                        options: {}
                    }
                ]
            }
        ]
    },
    plugins: [
        new webpack.ProvidePlugin({
            $: 'jquery',
            jQuery: 'jquery'
        })
    ]
};

module.exports = [mainConfig, rendererConfig];
```

!BUILD-DEV.bat

```bash
@echo off
title Main Dev
chcp 65001
cls
call ./node_modules/.bin/webpack --config webpack.dev.js
call ./node_modules/.bin/electron .
pause
```

