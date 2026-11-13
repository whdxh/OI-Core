# OI-Core 后端内核工程项目架构设计

## 一、 项目定位与核心目标

本项目是一个**完全独立的后端内核工程**。它不包含任何 UI 渲染代码，纯粹作为底层基础设施，为未来的前端 IDE 提供编译、沙盒评测、以及极速的补全数据检索功能。项目编译产物为头文件（`.hpp`）或独立评测可执行文件（`.exe`）。

## 二、 核心设计原则

1. **无 UI 纯内核**：本工程中绝不包含任何前端界面代码，只专注于底层的高并发、沙盒安全与极致性能。
2. **契约驱动（为前端准备）**：提供 `shared/` 目录作为数据契约。前端工程（未来的另一个独立项目）通过 `#include` 此目录下的头文件，与后端实现内存级的无缝对接。
3. **拒绝 JSON 的极速直传**：后端接收前端传来的纯字符串命令（如 `"g++ T1.cpp -o T1.exe -O2"`），直接执行；返回纯 C++ 结构体，彻底消除序列化开销。
4. **绝对沙盒隔离**：利用底层系统 API（如 Windows Job Objects），在运行学生代码时创建子进程进行限制，一旦死循环或超内存瞬间强杀，绝对保证宿主前端主进程的安全。

## 三、 后端工程目录结构

```text
OI-Core/
├── CMakeLists.txt              # 根构建脚本（生成后端 DLL 或 EXE）
│
├── shared/                     # 【暴露给前端的契约层】
│   ├── Common.hpp              # 核心数据结构（状态枚举、结果结构体）
│   └── Completion.hpp          # 补全数据字典（传给前端的数据结构）
│
├── include/                    # 【后端内部及对外接口定义】
│   ├── IBackend.hpp            # 总控接口（给前端调用的唯一窗口）
│   ├── ICompiler.hpp           # 编译调度接口
│   ├── ISandbox.hpp            # 沙盒运行接口
│   └── IJudger.hpp             # 文本比对接口
│
└── src/                        # 【后端具体实现】
    ├── core/
    │   ├── BackendImpl.cpp     # 统筹编译、沙盒、比对的主逻辑
    │   ├── CompilerImpl.cpp    # 抓取并静默执行外部编译器的实现
    │   └── JudgerImpl.cpp      # 忽略空格与CRLF差异的比对实现
    └── platform/
        └── SandboxWin.cpp      # Windows 底层 Job Objects 沙盒实现 (未来可扩展 Linux)
```

## 四、 前后端职责边界（后端视角）

**本后端工程需要实现的功能：**
1. **编译执行**：接收前端传来的编译字符串，静默调用系统环境，捕获 CE 报错信息并打包返回。
2. **沙盒防爆**：接收运行字符串，启动子进程，死循环强杀，返回 TLE/MLE 状态。
3. **精准判题**：接收前端传来的路径，进行文本比对，忽略脏字符。
4. **极速匹配**：接收前端传来的补全前缀，利用 C++ 算法在海量词库中瞬间匹配并返回对象数组。

**交由前端工程（未来开发）去干的活：**
1. 一切界面渲染、编辑器交互。
2. 拼装出符合规则的完整命令字符串发给后端。
3. 语法上下文合法性过滤、本地用户词频统计。

---

# OI-Core Backend Engine Architecture

## 1. Project Definition & Goals

This project is a **purely independent backend engine**. It contains NO UI rendering code. It acts strictly as underlying infrastructure, providing compilation, sandboxed execution, and ultra-fast completion data retrieval for future frontend IDEs. The compiled output is a dynamic link library (`.dll` / `.so`) or a standalone evaluation executable (`.exe`).

## 2. Core Design Principles

1. **UI-less Pure Core**: This repository contains NO frontend interface code, focusing solely on extreme performance, sandbox security, and high concurrency.
2. **Contract-Driven (For Frontend)**: Provides a `shared/` directory as a data contract. The future frontend project will `#include` headers from this directory for in-memory seamless integration.
3. **JSON-Free Ultra-Fast Transfer**: The backend receives raw string commands from the frontend (e.g., `"g++ T1.cpp -o T1.exe -O2"`) and executes them directly; it returns native C++ structs, eliminating all serialization overhead.
4. **Absolute Sandbox Isolation**: Uses low-level system APIs (e.g., Windows Job Objects) to spawn subprocesses when running user code. Infinite loops or memory overflows are instantly killed, guaranteeing the absolute safety of the host frontend process.

## 3. Backend Project Directory Structure

```text
OI-Core/
├── CMakeLists.txt              # Root build script (Builds DLL or EXE)
│
├── shared/                     # [Contract Layer Exposed to Frontend]
│   ├── Common.hpp              # Core data structures (Enums, Result structs)
│   └── Completion.hpp          # Completion data dictionary
│
├── include/                    # [Backend Interface Definitions]
│   ├── IBackend.hpp            # Main orchestrator interface
│   ├── ICompiler.hpp           # Compiler interface
│   ├── ISandbox.hpp            # Sandbox execution interface
│   └── IJudger.hpp             # Judger interface
│
└── src/                        # [Backend Implementations]
    ├── core/
    │   ├── BackendImpl.cpp     # Orchestrates compile, sandbox, and judge logic
    │   ├── CompilerImpl.cpp    # Captures and silently executes external compilers
    │   └── JudgerImpl.cpp      # Text comparison ignoring CRLF/trailing spaces
    └── platform/
        └── SandboxWin.cpp      # Windows Job Objects sandbox implementation
```

## 4. Responsibility Boundary

**What this Backend Project MUST do:**
1. **Compilation Execution**: Receive compile strings, silently invoke system tools, capture CE errors, and return.
2. **Sandbox Protection**: Receive run strings, spawn subprocesses, kill on time/memory limits, returning TLE/MLE statuses.
3. **Accurate Judging**: Compare output text, ignoring dirty characters like trailing spaces.
4. **Ultra-fast Matching**: Receive prefixes from the frontend, use C++ algorithms to instantly match and return object arrays from the dictionary.

**What the Frontend (Future Project) handles:**
1. All UI rendering and editor interactions.
2. Assembling raw command strings and sending them to the backend.
3. Syntax context filtering and local user word-frequency statistics.