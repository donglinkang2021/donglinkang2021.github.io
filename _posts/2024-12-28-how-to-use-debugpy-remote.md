---
title: 'How to use debugpy on remote server'
date: 2024-12-28
permalink: /posts/2024/12/how-to-use-debugpy-remote/
tags:
  - usage
---

**TL;DR:** This post explains how to use `debugpy` to debug Python scripts on a remote server using Visual Studio Code (VSCode). It covers installation, configuration of the VSCode launch settings, and how to connect to the remote script for debugging.

<!--more-->

1. Install debugpy

    ```bash
    pip install debugpy
    ```

2. Add the following content to `.vscode/launch.json`:

    ```json
    {
        "version": "0.2.0",
        "configurations": [
            {
                "name": "Python: Remote Attach",
                "type": "debugpy",
                "request": "attach",
                "listen": {
                    "host": "0.0.0.0",
                    "port": 5678
                },
                "pathMappings": [
                    {
                        "localRoot": "${workspaceFolder}", 
                        "remoteRoot": "."
                    }
                ]
            }
        ]
    }
    ```

3. Add one line to your entry python script:

    ```python
    import debugpy; debugpy.connect(('0.0.0.0', 5678))
    ```

4. Click the `Run and Debug`(<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>D</kbd>) button in VSCode, and select `Python: Remote Attach`(<kbd>F5</kbd>). Here, the `Python: Remote Attach` configuration is the one we just added to `.vscode/launch.json`. Moreover, the host and port in the configuration should be the same as the one in the script.

5. After click the debug button, add breakpoints in your code and run the script. The script will stop at the breakpoints and you can debug it in VSCode.

reference: [Debugpy——如何使用VSCode调试无法直接执行的Python程序 - 知乎](https://zhuanlan.zhihu.com/p/560405414)
