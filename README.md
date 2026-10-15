# Core-x86-Disassembler

滴水逆向硬编码学习项目：用 C 实现的 x86 指令解码器，以及读取机器码数组或 PE32 代码节的命令行演示。TinyX86 通过本仓库的 `CoreX86` 静态库复用相同的解码实现。

解码器解析前缀、Opcode、ModR/M、SIB、位移、立即数与部分 x87 指令，输出 `DecodeContext` 和汇编文本。它覆盖教学中实现的指令子集，并非完整 x86/x86-64 解码器。

## 下载与构建

```powershell
git clone --recurse-submodules https://github.com/582835293/Core-x86-Disassembler.git
cd Core-x86-Disassembler
# 在 Visual Studio Developer PowerShell 中运行；也可直接打开仓库内 .slnx
msbuild "反汇编引擎v1.0.vcxproj" /m /p:Configuration=Debug /p:Platform=Win32
# VS 2022 工具集可追加 /p:PlatformToolset=v143
```

安装 Visual Studio C++ 桌面开发组件和 Windows 10 SDK，选择 x86/Win32。CMake 构建与验证：

```powershell
cmake -S . -B build -A Win32
cmake --build build --config Debug --parallel
ctest --test-dir build -C Debug --output-on-failure
```

完整应用当前只支持 Windows x86/PE32。[共享依赖、升级方法和项目关系](docs/project-relationships.md)。

## 模块

| 文件/目标 | 职责 |
| --- | --- |
| `disasm.c` / `disasm.h`、`CoreX86.vcxproj` | 可复用解码静态库，无 PETools 依赖 |
| `反汇编引擎.c`、`反汇编引擎v1.0.vcxproj` | 命令行演示；PE 输入依赖 `deps/PETools` |
| `tests/disasm_tests.c` | 前缀、寻址、立即数、格式化与共享接口回归测试 |

独立测试解码核心可用 `cmake -S . -B build -DCORE_X86_BUILD_DEMO=OFF`。演示默认使用内置数组；`RUN_MODE` 和 `TARGET_PATH` 在演示源文件顶部设置。

公共调用契约见 [解码器接口](docs/decoder-api.md)，原理笔记见 [x86 反汇编引擎](x86反汇编引擎.md)。许可证沿用仓库已有 [MIT 文本](LICENSE.txt)。
