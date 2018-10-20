## 技术栈

- Node.js
- Electron
- Typescript
- Jest
- 

## 安装步骤

```bash
npm init
```

备注：入口为dist/main.js

```bash
npm i -D typescript electron
mkdir src
mkdir dist
```

## 编辑初始文件

### src\main.ts

```typescript
import { app, BrowserWindow } from 'electron'
let win: BrowserWindow = null
// 启动前端
startFront()
// 启动脚本
function startFront() {
    app.on('ready', () => { // 程序启动
        createWindow()
    })
    app.on('window-all-closed', () => { // 窗口已全关闭
        // 退出程序
        console.log('[DEBG]检测到窗口全部关闭');
        app.quit()
    })
}
function createWindow() {
    win = new BrowserWindow({ width: 1024, height: 768 })
    win.loadFile('src/index.html')
    win.webContents.openDevTools()
    Menu.setApplicationMenu(null)
    win.on('closed', () => {
        win = null
        console.log('[DEBG]检测到窗口关闭');
    })
}
```

### src\index.html

```html
<!DOCTYPE html>
<html style='height:100%'>

<head>
    <meta charset="UTF-8">
    <title>TITLE</title>
</head>

<body style='height:100%; margin: 0px'>
    <div id="root" style='height:100%'>
    </div>
    <!-- <script type="text/javascript" src="dist/renderer.js"></script> -->
</body>

</html>
```

## 编辑配置文件

### tsconfig.json

```json
{
    "compilerOptions": {
        "outDir": "./dist",
        "allowJs": true,
        "target": "es5"
    },
    "include": [
        "./src/**/*"
    ]
}
```

### !Debug.bat

```bash
@echo off
chcp 65001
cls
title Debug
echo 尝试编译Typescript…
call tsc
echo 尝试启动Electron…
.\node_modules\.bin\electron .
pause
```

## 测试启动

```bash
.\!Debug.bat
```

