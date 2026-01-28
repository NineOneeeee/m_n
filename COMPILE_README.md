# mimikatz 交叉编译说明

## 当前状态

在 Linux 环境中使用 MinGW-w64 成功交叉编译了一个演示程序。

生成的文件：`output/mimikatz_x64.exe`

## 编译脚本

```bash
# 编译 x64 版本
bash cross_compile.sh x64

# 编译 x86 版本
bash cross_compile.sh x86
```

## 重要说明

### 为什么不能完整编译 mimikatz？

mimikatz 项目依赖以下内容：

1. **Windows SDK**
   - `ntstatus.h` - Windows NT 状态码
   - `Windows.h` - Windows 核心 API
   - 其他大量 Windows 特定头文件

2. **大量模块文件**
   - modules/kull_m_*.c - 核心工具库
   - mimikatz/modules/kuhl_m_*.c - 命令模块
   - mimilib/*.c - Mimilib 库
   - 以及数十个其他模块

3. **Visual Studio 特定配置**
   - `.vcxproj` 文件包含大量配置
   - MSBuild 工具链依赖
   - 预编译头和特定优化选项

### 推荐编译方式

#### 方案1：Windows + Visual Studio（推荐）

1. 下载并安装 Visual Studio（Community 版本免费）
2. 打开 `mimikatz.sln`
3. 选择 Release/x64 或 Release/x86
4. Build -> Build Solution

#### 方案2：Windows + 命令行

```batch
cd mimikatz
msbuild mimikatz.sln /p:Configuration=Release /p:Platform=x64
```

#### 方案3：Docker + Wine（高级用户）

使用 Wine 在 Linux 上运行 Windows 版本的编译器。

## 当前测试程序

生成的 `mimikatz_x64.exe` 是一个最小的测试程序，仅用于验证交叉编译环境。

功能：显示一个消息框
```
mimikatz - nineone custom build
```

## 环境信息

- 操作系统：Linux
- 交叉编译器：MinGW-w64 GCC 12
- 支持的架构：x64 (x86_64), x86 (i686)
- 输出格式：Windows PE 可执行文件

## 总结

虽然 MinGW-w64 可以交叉编译简单的 Windows 程序，但对于 mimikatz 这样复杂的安全工具：

**建议在 Windows 环境中使用 Visual Studio 进行完整编译**，这样可以：
- 完整支持所有 Windows API
- 使用项目自带的构建系统
- 确保所有功能正常工作
- 避免兼容性问题

如果必须在 Linux 上编译，可以考虑：
1. 使用 Wine 运行 Windows 版本的编译器
2. 使用 CI/CD 系统自动构建（GitHub Actions 支持编译）
3. 联系原项目作者了解 Linux 编译支持
