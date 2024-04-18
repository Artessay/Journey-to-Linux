## GDB

### Visual Studio Code中的使用

在VSCode中，打开任意C、C++文件，并点击左侧的`Run and Debug`按钮（快捷键：`Ctrl + Shift + D`）。

点击创建`launch.json`文件。

在你点击之后会有默认的模板，其中参数的含义如下：

* program 要调试的程序名（包含路径，最好绝对路径，免得麻烦）
* miDebuggerServerAddress 服务器的地址和端口 （一般用不到）
* cwd 调试程序的路径
* miDebuggerPath gdb 的路径

lauch方式的配置：

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(gdb) Launch",  // 该调试任务的名字，启动调试时会在待选列表中显示
            "type": "cppdbg",
            "request": "launch",
            "program": "${fileDirname}\\${fileBasenameNoExtension}.exe",
            "args": [],
            "stopAtEntry": false,  // 这一项控制是否在入口处暂停，默认false不暂停，改为true暂停
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": false,  // 这一项控制是否启动外部控制台（独立的黑框）运行程序，默认false表示在集成终端中运行
            "MIMode": "gdb",
            "miDebuggerPath": "C:\\mingw64\\bin\\gdb.exe",  // 调试器路径，必须与你自己的电脑相符
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
            "preLaunchTask": "C/C++: gcc.exe build active file"  // 调试前的预执行任务，这里的值是tasks.json文件中对应的编译任务，也就是调试前需要先编译
        }
    ]
}
```

attach方式的配置：

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(gdb) Attach",  // 该调试任务的名字，启动调试时会在待选列表中显示
            "type": "cppdbg",
            "request": "attach",
            "program": "${fileDirname}\\${fileBasenameNoExtension}.exe",
            "processId": "${command:pickProcess}",
            "miDebuggerPath": "C:\\mingw64\\bin\\gdb.exe",  // 调试器路径，必须与你自己的电脑相符
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ]
        }
    ]
}
```

有时候，我们可能需要`sudo`权限才能执行gdb，而单纯在VSCode中无法配置完成这一点。。因此，我们可以在gdb_util目录下创建一个sgdb文件，并将`launch.json`中的"miDebuggerPath"改为sgdb的文件路径即可。

其中，sgdb的内容如下：

```bash
#!/bin/bash

sudo gdb $@
```
