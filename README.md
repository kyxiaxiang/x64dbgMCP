<h1 align="center"><b>x64dbg MCP</b></h1>

<p align="center">
  <img src="https://img.shields.io/github/stars/kyxiaxiang/x64dbgmcp?style=social" />
  <img src="https://img.shields.io/github/downloads/kyxiaxiang/x64dbgmcp/total?style=social" />
</p>

<div align="center">基于 <a href="https://github.com/Wasdubya/x64dbgMCP">Wasdubya/x64dbgMCP</a> 的改进版本。<br>通过 MCP 协议将大语言模型（Claude、Cursor 等）与 x64dbg 调试器桥接，实现通过自然语言直接操控调试器。</div>

<br>

## 改进内容

相较于原始项目，本 fork 做了以下改进：

- **监听 0.0.0.0** — 支持从局域网内其他设备远程连接，不再局限于本机
- **多线程请求处理** — 每个连接独立线程，单个耗时操作不会阻塞整个服务器
- **修复 Pattern/FindMem bug** — 原代码去除空格逻辑有误，导致模式匹配失效
- **未知路径返回 404** — 不再静默关闭连接，便于客户端排查问题
- **静态链接 MSVC 运行库** — 无需安装 Visual C++ Redistributable 即可使用
- **GitHub Actions 自动构建** — 推送 tag 自动编译并发布 `.dp32` / `.dp64`

<br>

## 功能特性

- **40+ x64dbg SDK 工具** — 覆盖几乎所有调试功能：寄存器、内存、断点、汇编、符号等
- **跨架构支持** — 同时支持 x64dbg（64位）和 x32dbg（32位）
- **远程调试** — 插件运行在 Windows 调试机上，MCP 客户端可在任意设备上连接

<br>

## 快速上手

### 1. 下载插件

从 [Releases](../../releases) 页面下载对应架构的插件文件：

| 文件 | 适用 |
|------|------|
| `MCPx64dbg.dp64` | x64dbg（64位调试） |
| `MCPx64dbg.dp32` | x32dbg（32位调试） |

复制到对应目录：
```
[x64dbg安装目录]/release/x64/plugins/MCPx64dbg.dp64
[x64dbg安装目录]/release/x32/plugins/MCPx64dbg.dp32
```

### 2. 安装 Python 依赖

```bash
pip install mcp requests
```

### 3. 配置 MCP 客户端

将 `x64dbg.py` 添加到你的 MCP 客户端配置中。以 Claude Desktop 为例：

```json
{
  "mcpServers": {
    "x64dbg": {
      "command": "python",
      "args": [
        "path/to/x64dbg.py",
        "http://<调试机IP>:8888/"
      ]
    }
  }
}
```

本机调试时地址填 `http://127.0.0.1:8888/`，远程调试时替换为调试机的 IP。

也可通过环境变量指定：
```bash
set X64DBG_URL=http://192.168.1.100:8888/
```

### 4. 开始调试

1. 启动 x64dbg，加载目标程序
2. 按 `ALT+L` 确认插件日志中出现 `HTTP server started at http://0.0.0.0:8888/`
3. 启动 MCP 客户端，即可通过自然语言操控调试器

<br>

## 使用示例

```
查看当前 RAX 和 RIP 寄存器的值
```
```
在当前模块中搜索特征码 48 8B 05 ?? ?? ?? ??
```
```
在 0x401000 处设置断点，然后运行程序
```
```
读取 RCX 指向地址处的 64 字节内存
```

> **提示**：提前告知模型目标程序的完整路径，便于程序崩溃时自动重新加载。建议先调用 `GetModuleBase` 获取基址，再进行后续操作。

<br>

## 从源码构建

需要 CMake 和 MSVC（Visual Studio 2019+）：

```bash
git clone https://github.com/kyxiaxiang/x64dbgMCP
cd x64dbgMCP
cmake -S . -B build -DX64DBG_DOWNLOAD_SDK=ON
cmake --build build --target all_plugins --config Release
```

构建产物：
- `build/build64/Release/MCPx64dbg.dp64`
- `build/build32/Release/MCPx64dbg.dp32`

<br>

## 致谢

本项目基于 [Wasdubya/x64dbgMCP](https://github.com/Wasdubya/x64dbgMCP) 开发，感谢原作者的贡献。
