
# 快捷键
## **Meta-Shift-P**
该快捷键会弹出命令框，用于输入各种命令，为了方便用户使用，命令一般都不需要输入全部信息，一般情况输入一部分信息就找到自己想要的命令。

# 扩展

## Remote-SSH

修改配置指令
- **Meta-Shift-P> Open SSH Config RET /Users/username/.ssh/config**

其中**/Users/username/.ssh/config**是MAC平台的配置文件路径。

配置文件内容如下：
```
# Read more about SSH config files: https://linux.die.net/man/5/ssh_config
Host 1.1.1.1
    HostName myhost
    User user
    Port 22

Host git.company.com
    HostName git.company.com
    User xxx@company.com
    Port 22
    IdentityFile ~/.ssh/user_rsa
    PubkeyAcceptedKeyTypes +ssh-rsa
					   
```

## Project Manager
如果需要管理多个项目，可能需要安装“Project Manager”扩展  

首先，需要修改创建配置文件：
- **Meta-Shift-P> Edit Projects**

``` json
[
	{
        "name": "opencv",
        "rootPath": "vscode-remote://ssh-remote+myhost//data/project/third/opencv",
        "paths": [],
        "tags": [],
        "enabled": true
	},
	{
       "name": "xxx2",
       "rootPath": "vscode-remote://ssh-remote+myhost/data/docker/xxx2",
       "paths": [
       	"/data/docker/.cache/xxx/68cf8000274feafda46a154445f16a5e/external",
       	"/opt/rh/devtoolset-7/root/usr/"
       ],
       "tags": [],
       "enabled": true
	}
]
```

使用时用下面的命令打开项目
- **Meta-Shift-P> List Projects to Open**


# 运行和调试

在工作目录下创建配置文件**.vscode/launch.json**

## C/C++
``` json
{
    "version": "0.2.0",
    "configurations": [
	{
	    "name": "Run your Program",
	    "type": "cppdbg",
	    "request": "launch",
	    "program": "/yousr/program/filepath",
	    "args": ["arg1", "arg2", "arg3"],
	    "stopAtEntry": true,
	    "cwd": "${workspaceRoot}",
	    "environment": [],
	    "internalConsoleOptions": "openOnSessionStart",
	    "MIMode": "gdb",
	    "miDebuggerPath": "/usr/bin/gdb",
	    "externalConsole": false,
	    "setupCommands": [
		{
		    "description": "Enable pretty-printing for gdb",
		    "text": "-enable-pretty-printing",
		    "ignoreFailures": true
		}
	    ]
	},
	{
	    "name": "Attach your Program",
	    "type": "cppdbg",
	    "request": "attach",
	    "program": "/yousr/program/filepath",
	    "stopAtEntry": true,
	    "cwd": "${workspaceRoot}",
	    "environment": [],
	    "internalConsoleOptions": "openOnSessionStart",
	    "MIMode": "gdb",
	    "miDebuggerPath": "/usr/bin/gdb",
	    "externalConsole": true,
	    "processId": "${command:pickProcess}",
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

## lua
需要安装扩展**Lua**和**LuaPanda**。其中**LuaPanda**可用于单步调试lua脚本程序。
```
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
	{
	    "type": "lua",
	    "request": "launch",
	    "tag": "independent_file",
	    "name": "LuaPanda-IndependentFile",
	    "description": "独立文件调试模式,使用前请参考文档",
	    "luaPath": "",
	    "packagePath": [
			"/data/project/yourproject/develop/src/?.lua",
			"/data/project/yourproject/develop/lib/?.lua",
			"/data/project/yourproject/develop/spec/?.lua",
			"/data/project/yourproject/develop/data/?.lua",
			""
	    ],
	    "luaFileExtension": "",
	    "connectionPort": 8820,
	    "stopOnEntry": false,
	    "useCHook": true,
	},
	{
	    "type": "lua",
	    "request": "launch",
	    "tag": "normal",
	    "name": "LuaPanda",
	    "description": "通用模式,通常调试项目请选择此模式 | launchVer:3.2.0",
	    "cwd": "${workspaceFolder}",
	    "luaFileExtension": "",
	    "connectionPort": 8818,
	    "stopOnEntry": true,
	    "useCHook": true,
	    "autoPathMode": true,
	},
    ]
}
```
