---
layout: post
title: vsCode 运行 python
categories: 
- vsCode
- python
description: vsCode 配置 python 运行环境( virtual-environment)
keywords: vsCode, python
---

# VS Code Python 虚拟环境配置

## 创建虚拟环境

1. 安装virtualenv
``` python3
pip install virtualenv
```
2. 创建虚拟环境
``` python3
python -m venv <虚拟环境名(英文)>
``` 
3. 启动虚拟环境
执行**.\Scripts\activate**


## VS Code项目设置为虚拟环境
- 进入<kbd>Settings</kbd>, 搜索python.venv.  
- **Venv Folders**为虚拟环境名
- **Venv Path**为虚拟环境所在目录
- <kbd>Shift</kbd>+<kbd>Ctrl</kbd>+<kbd>p</kbd> 搜索python:Select Interpreter选择虚拟环境下的解释器


### Note
如果
**& D:/project/pyproject/hotRollingProject/venv/Scripts/Activate.ps1**出错
则在管理员打开Powershell执行, 默认策略为Restricted
``` 
set-ExecutionPolicy RemoteSigned
```

