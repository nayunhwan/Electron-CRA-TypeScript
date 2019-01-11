# Electron Create-React-App TypeScript

This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app) and TypeScript.

## How to make this boilerplate

> You can also just clone this repository. But if you just clone and use this repository, may be regacy project. So I recommend you following this description.

At First, Bootstrap Project using [Create React App](https://github.com/facebook/create-react-app).

```sh
$ npx create-react-app <YOUR-PROJECT-NAME> --typescript
$ cd <YOUR-PROJECT-NAME>
```

Install **Electron**

```
$ yarn add -D electron
```

Create `electron-starter.js` in Root Directory

> Note: Don't use **.ts** extension. YOU MUST USE **.js** EXTENSION.

```js
// Modules to control application life and create native browser window
const { app, BrowserWindow } = require("electron");
const url = require("url");
const path = require("path");

// Keep a global reference of the window object, if you don't, the window will
// be closed automatically when the JavaScript object is garbage collected.
let mainWindow;

function createWindow() {
  // Create the browser window.
  mainWindow = new BrowserWindow({ width: 800, height: 600 });

  // and load the index.html of the app.
  const startUrl =
    process.env.ELECTRON_START_URL ||
    url.format({
      pathname: path.join(__dirname, "/../build/index.html"),
      protocol: "file:",
      slashes: true
    });
  mainWindow.loadURL(startUrl);

  // Open the DevTools.
  // mainWindow.webContents.openDevTools()

  // Emitted when the window is closed.
  mainWindow.on("closed", function() {
    // Dereference the window object, usually you would store windows
    // in an array if your app supports multi windows, this is the time
    // when you should delete the corresponding element.
    mainWindow = null;
  });
}

// This method will be called when Electron has finished
// initialization and is ready to create browser windows.
// Some APIs can only be used after this event occurs.
app.on("ready", createWindow);

// Quit when all windows are closed.
app.on("window-all-closed", function() {
  // On macOS it is common for applications and their menu bar
  // to stay active until the user quits explicitly with Cmd + Q
  if (process.platform !== "darwin") {
    app.quit();
  }
});

app.on("activate", function() {
  // On macOS it's common to re-create a window in the app when the
  // dock icon is clicked and there are no other windows open.
  if (mainWindow === null) {
    createWindow();
  }
});

// In this file you can include the rest of your app's specific main process
// code. You can also put them in separate files and require them here.
```

Also Create `electron-wait-react.js` in Root Directory.

> Note: Don't use **.ts** extension. YOU MUST USE **.js** EXTENSION.

```js
const net = require("net");
const port = process.env.PORT ? process.env.PORT - 100 : 3000;

process.env.ELECTRON_START_URL = `http://localhost:${port}`;

const client = new net.Socket();

let startedElectron = false;
const tryConnection = () =>
  client.connect(
    { port: port },
    () => {
      client.end();
      if (!startedElectron) {
        console.log("starting electron");
        startedElectron = true;
        const exec = require("child_process").exec;
        exec("npm run electron");
      }
    }
  );

tryConnection();

client.on("error", (error) => {
  setTimeout(tryConnection, 1000);
});
```

Install `foreman` and `cross-env`

```sh
$ yarn add -D foreman cross-env
```

Edit `package.json`

```json
...
"main": "./electron-starter.js",
"scripts": {
  "start": "cross-env BROWSER=none nf start -p 3000",
  "build": "react-scripts build",
  "test": "react-scripts test",
  "eject": "react-scripts eject",
  "electron": "electron .",
  "electron-start": "node ./electron-wait-react",
  "react-start": "react-scripts start"
},
```

Create `Procfile` in Root Directory.

```
react: npm run react-start
electron: npm run electron-start
```

## Run

```sh
$ yarn start
```

## Referenced

https://gist.github.com/matthewjberger/6f42452cb1a2253667942d333ff53404#prerequisites

## LICENSE

[MIT Licensed](./LICENSE)
