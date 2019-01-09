---
layout: post
category: ""
title:  "Cannot find module 'vscode' in developpemnt mode (f5)"
tags: [vscode]
---


Copy from https://github.com/SqrTT/prophet/issues/142

Run the command: npm install
Also please make sure you are running vscode instance an not just run extension as node program.

```

// launch.json

 {
    "version": "0.2.0",
    "configurations": [
        {
            "type": "extensionHost",
            "request": "launch",
            "name": "Launch Extension",
            "runtimeExecutable": "${execPath}",
            "args": [
                "--extensionDevelopmentPath=${workspaceFolder}"
            ],
            "outFiles": [
                "${workspaceFolder}/out/**/*.js"
            ]
        }
    ]
}

```
