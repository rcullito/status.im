---
id: desktop
title: Build Desktop
---

# Build Status Desktop for Yourself!

## Prerequisites

You will need the following tools installed:
  - Node.js v.8
  - CMake 3.1.0 or higher
  - Additional packages: `extra-cmake-modules`; Keychain access on `Linux` requires `libgnome-keyring0`.
  ```
  Linux:
sudo apt install extra-cmake-modules libgnome-keyring0

MacOS:
brew install kde-mac/kde/kf5-extra-cmake-modules
  ```
  - Qt 5.9.1 or higher. You'll only need macOS and QtWebEngine components installed. 


## Qt setup

Set Qt's environment variables: 
  - `QT_PATH` should point to the location of Qt's distribution, e.g. `/Users/<user_name>/Qt/5.11.2`. It should not end with a slash.
  - add qmake to PATH via 
    - On MacOS: `export PATH=<QT_PATH>/clang_64/bin:$PATH`
    - On Linux: `export PATH=<QT_PATH>/gcc_64/bin:$PATH`

OS-specific notes:
  - If building on Ubuntu newer than 16.10, then Qt 5.10.1 is recommended (although not fully tested).
  - Note that building on macOS Mojave requires Qt 5.11.2, lower versions will not work.

Qt 5.9.1 for Linux is available here: https://download.qt.io/archive/qt/5.9/5.9.1/qt-opensource-linux-x64-5.9.1.run

## Caveats

- If npm hangs at some step, check the version. If it's 5.6.0 or higher, try downgrading to 5.5.1 via `npm install -g npm@5.5.1`

# Building a release package

Run the following commands to build a Desktop package for the host environment (Linux or Mac OS):

``` bash
git clone https://github.com/status-im/status-react.git
cd status-react
make setup
make prepare-desktop
make release-desktop
```

# Development environment setup

## To install react-native-cli with desktop commands support

``` bash
git clone https://github.com/status-im/react-native-desktop.git
cd react-native-desktop/react-native-cli
npm update
npm install -g
```

## To setup re-natal dev builds of status-react for Desktop

1. Run the following commands:
    ``` bash
    git clone https://github.com/status-im/status-react.git
    cd status-react
    ```
1. In separate terminal tab: `npm start` (note: it starts react-native packager )
1. In separate terminal tab: `node ./ubuntu-server.js`
1. In separate terminal tab: `clj -m clj-rn.main watch --platform desktop` (note: wait until sources are compiled)
1. In separate terminal tab: `react-native run-desktop`

## Clean up data

To completely clean up data from previous development sessions, such as accounts, you need to do the following:

### On Linux

``` bash
# First kill the `ubuntu-server` process because it has a cache of realm db
killall ubuntu-server

# Then remove data and cache folders
rm -rf ~/.local/share/StatusIm \
       ~/.cache/StatusIm \
       $STATUS_REACT_HOME/status-react/default.realm*
```

### On a Mac

Go to `~/Library/Application Support/` and delete any Status directories. Delete the app in `/Application`. Then reinstall.

## Editor setup

Running `clj -m clj-rn.main watch --platform desktop` will run a REPL on port 7888 by default. Some additional steps might be needed to connect to it.

### emacs-cider

In order to get REPL working, put the following config in `.dir-locals.el` :

``` elisp
((nil . ((cider-cljs-repl-types . ((figwheel-repl "(do (require 'figwheel-sidecar.repl-api) (figwheel-sidecar.repl-api/cljs-repl))"
                                                  cider-check-figwheel-requirements)))
         (cider-default-cljs-repl . figwheel-repl))))
```

Then connect to the repl with `cider-connect-cljs` (default is localhost on port 7888)

### vim-fireplace

For some reason there is no `.nrepl-port` file in project root, so `vim-fireplace` will not be able to connect automatically. You can either:

- run `:Connect` and answer a couple of interactive prompts
- create `.nrepl-port` file manually and add a single line containing `7888` (or whatever port REPL is running on)

After Figwheel has connected to the app, run the following command inside Vim, and you should be all set:

``` clojure
:Piggieback (figwheel-sidecar.repl-api/repl-env)
```