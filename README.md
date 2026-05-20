<h1 align="center"><b> x64dbg MCP </b></h1>

<p align="center">
  <a href="https://www.buymeacoffee.com/WASDUBYA" target="_blank">
    <img src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" alt="请我喝杯咖啡" width="120"/>
  </a>
</p>
<p align="center">
  <img src="https://img.shields.io/github/stars/wasdubya/x64dbgmcp?style=social" />
  <img src="https://img.shields.io/github/downloads/WASDUBYA/x64dbgmcp/total?style=social" />
</p>

<div align="center">一个 MCP 服务器，可将各种大语言模型（已测试 Claude 和 Cursor）与 x64dbg 调试器桥接，通过提示词直接访问调试功能。</div>

<h2 align="center"><b>功能特性（从源码构建以获取最新版本）</b></h2>

- **40+ x64dbg SDK 工具** - 提供对几乎所有 SDK 调试功能的访问，实现智能调试。
- **跨架构支持** - 同时支持 x64dbg 和 x32dbg。
- **API 兼容性** - 可通过 Python 文件中给定的参数从命令行运行。
- **监听 0.0.0.0** - 支持来自局域网内其他设备的远程连接。
<br><br>

<h2>三步快速上手</h2>

1. **下载插件**
   - 从本仓库的 [Releases](../../releases) 页面下载 `.dp64` 或 `.dp32`
   - 复制到本地目录：`[x64dbg安装目录]/release/x64/plugins/`

2. **配置 Claude Desktop**
   - 从本仓库 `src` 目录复制 `x64dbg.py`，并执行 `pip install mcp requests`
   - 更新本地 `claude_desktop_config.json`，填入 `x64dbg.py` 的路径

```json
{
  "mcpServers": {
    "x64dbg": {
      "command": "Python解释器路径",
      "args": [
        "x64dbg.py的路径"
      ]
    }
  }
}
```

3. **开始调试**
   - 启动 x64dbg
   - 启动 Claude Desktop
   - 确认插件加载成功（在 x64dbg 中按 ALT+L 查看日志）
<br>

<h2>远程连接</h2>

插件现在监听 `0.0.0.0:8888`，可接受来自局域网内任意设备的连接。
若要从另一台机器连接，在启动 MCP 客户端时通过参数或环境变量指定地址：

```bash
# 通过命令行参数
python x64dbg.py http://192.168.1.100:8888/

# 或通过环境变量
set X64DBG_URL=http://192.168.1.100:8888/
python x64dbg.py
```

**安全提示**：监听 `0.0.0.0` 意味着局域网内的设备均可连接调试器。请确保在受信任的网络环境下使用。
<br>

<h2>从源码构建（获取最新功能）</h2>

** 需要安装 CMake 和 MSVC 并加入 PATH **

```bash
git clone https://github.com/kyxiaxiang/x64dbgMCP
cd x64dbgMCP
cmake -S . -B build -DX64DBG_DOWNLOAD_SDK=ON
cmake --build build --target all_plugins --config Release
```

**提示**

1. 使用 `--target all_plugins` 同时构建 x32 和 x64 版本，或使用 `-A` 标志单独指定架构。例如，32 位构建：
```bash
cmake -S . -B build32 -A Win32 -DBUILD_BOTH_ARCHES=OFF
cmake --build build32 --config Release
```
<br>

<h2>使用示例</h2>

**查看寄存器：**
```
"RAX 和 RIP 寄存器的当前值是多少？"
```

**搜索特征码：**
```
"在当前模块中查找特征码 '48 8B 05'"
```

**[Cursor 使用示例](https://github.com/Wasdubya/x64dbgMCP/blob/main/Cursor.Opus4.5-Find-PEB.md)**
<br><br>

**更多使用技巧**
- 如果不向模型提供可执行文件的路径，当程序崩溃或挂起时，模型将无法自动重启它。请提供完整的绝对路径，以便模型调用 `CMDEXEC` 函数，例如：`init C:\绝对路径\到\程序.exe`。
- 建议从 `GetModuleBase` 开始，让模型查询正确的地址——x64dbg 不会响应超出范围的地址。
<br><br>

<div align="center">🧀 <b>享受自动化调试的乐趣</b> 🛎️</div>
