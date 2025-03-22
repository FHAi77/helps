
# 相关视频 [Python开发环境部署教程](https://youtu.be/rOfzKI0syPE)


本教程将详细介绍如何在 Windows 系统上配置 Python 开发环境，包括安装 Python、配置虚拟环境以及使用 VS Code 进行开发，适合新手和需要精细配置的开发者。

---

## **1. 安装 Python**

### 1.1 下载 Python
1. 访问 [Python 官网](https://www.python.org/downloads/).
2. 选择最新版本的 Python 进行下载（建议下载 64-bit 版本）。

### 1.2 判断选择 64 位还是 32 位 Python
- **查看操作系统架构**：
  1. 打开 **设置**，选择 **系统**，然后点击 **关于**。
  2. 在 **系统类型** 中，你会看到是 32 位还是 64 位操作系统。

### 1.3 安装步骤
1. **运行安装程序**：双击下载的 `.exe` 文件开始安装。
2. 在安装界面的底部，确保勾选 **Add Python to PATH**，这是确保可以从命令行直接运行 Python 的重要步骤。
3. 点击 **Customize installation** 进入自定义安装配置界面。

### 1.4 验证安装
1. 安装完成后，打开 **命令行**（按下 `Win + R`，输入 `cmd`）并输入以下命令验证安装：
   ```bash
   python --version
   pip --version
   ```
   如果安装成功，命令行会显示 Python 和 pip 的版本号。

---

## **2. 配置国内镜像源（提高安装速度）**
为了提高安装 Python 包的速度，特别是使用 `pip` 安装时，可以配置国内镜像源。

### 2.1 配置镜像源
1. 打开命令行并运行以下命令，设置 `pip` 使用清华大学的镜像源：
   ```bash
   pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
   ```

### 2.2 验证配置
运行以下命令检查镜像源配置是否生效：
```bash
pip config list
```

---

## **3. 创建虚拟环境**

### 3.1 选择工作目录
首先，在文件资源管理器中选择一个目录作为项目的存放位置（例如 `D:\testing`）。

### 3.2 创建虚拟环境
1. 打开命令行，进入项目目录：
   ```bash
   cd D:\testing
   ```
2. 运行以下命令创建虚拟环境：
   ```bash
   python -m venv venv
   ```
   该命令会在当前目录下创建一个名为 `venv` 的文件夹，里面包含独立的 Python 解释器和相关库。


### 3.3 激活虚拟环境
1. 在命令行中运行以下命令激活虚拟环境：
   ```bash
   venv\Scripts\activate
   ```
2. 激活成功后，命令行提示符前会显示 `(venv)`，这表示虚拟环境已生效。

### 3.5 退出虚拟环境
当你完成工作后，可以通过以下命令退出虚拟环境：
```bash
deactivate
```

---

## **4. 安装项目依赖**

### 4.1 在虚拟环境中安装依赖
1. 激活虚拟环境后，安装所需的 Python 包。例如：
   ```bash
   pip install numpy pandas
   ```

### 4.2 导出依赖列表
当项目中需要与他人共享依赖时，可以使用以下命令将当前环境的依赖保存到 `requirements.txt` 文件中：
```bash
pip freeze > requirements.txt
```

### 4.3 使用依赖列表安装
1. 在新环境中运行以下命令安装依赖：
   ```bash
   pip install -r requirements.txt
   ```

---

## **5. 配置 VS Code（推荐 IDE）**

### 5.1 安装 VS Code
1. 访问 [VS Code 官网](https://code.visualstudio.com/) 下载并安装 VS Code 编辑器。

### 5.2 安装 Python 插件
1. 启动 VS Code，点击左侧边栏的 **扩展** 按钮（或按下 `Ctrl+Shift+X`），搜索并安装 **Python** 插件。

### 5.3 配置虚拟环境
1. 打开你的项目目录。
2. 按 `Ctrl+Shift+P` 打开命令面板。
3. 输入并选择 **Python: Select Interpreter**。
4. 在弹出的列表中，选择 `venv\Scripts\python.exe` 作为解释器。这样 VS Code 会使用你在虚拟环境中安装的 Python 版本。
   
   如果虚拟环境未显示，点击 **Enter Interpreter Path**，然后选择 `venv\Scripts\python.exe` 文件的路径。

### 5.4 配置 VS Code 自动激活虚拟环境
1. 可以设置 VS Code 自动激活虚拟环境。打开命令面板，输入 **Preferences: Open User Settings**。
2. 搜索 `python.terminal.activateEnvironment`，确保它被勾选启用，这样每次启动 VS Code 时，虚拟环境会自动激活。

### 5.5 测试运行
1. 在 VS Code 中新建一个 Python 文件（如 `main.py`），输入以下代码：
   ```python
   print("Hello, World!")
   ```
2. 在 VS Code 中按 `F5` 或点击上方的绿色播放按钮运行代码，确保输出正常。

---

## **6. 常见问题及解决方法**

### 6.1 激活虚拟环境失败
如果遇到 **“无法加载脚本文件”** 的错误，可以尝试以下解决方法：
1. 打开命令行，输入以下命令：
   ```bash
   Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
   ```
   这会允许执行 PowerShell 脚本，解决权限问题。

### 6.2 包安装失败或速度慢
- 确保已配置国内镜像源。如果依然存在问题，可以尝试使用其他镜像源，如阿里云或清华大学等。
- 检查网络连接是否稳定。

### 6.3 VS Code 无法识别虚拟环境
- 确保已正确选择虚拟环境的解释器。
- 如果问题仍然存在，尝试重启 VS Code。

---
