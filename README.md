# OI-Core

[![Status](https://img.shields.io/badge/status-WIP-orange.svg)]()
[![Language](https://img.shields.io/badge/language-C%2B%2B17-blue.svg)]()
[![License](https://img.shields.io/badge/license-MIT-green.svg)]()

**⚠️ This project is currently under active development (WIP) and is not yet ready for production use.**

A high-performance, fully offline local evaluation engine for competitive programming (OI/ICPC).

## 🚀 Vision
Existing tools like Dev-C++ are outdated. We are building a modern, lightweight, and offline evaluation core engine designed to provide a seamless competitive programming experience.

## ✨ Features & Roadmap
- [x] **Extensible Language Support**: Implemented via pure C++ OOP (Inheritance & Polymorphism). Developers can easily extend new languages (C++, Python, Rust) by subclassing the base `Language` class, completely eliminating the need for external config parsing.
- [ ] **Sandbox Execution**: Strict time and memory limits (TLE/MLE protection) using Windows Job Objects and Linux `setrlimit`.
- [ ] **Smart Judger**: Ignore trailing spaces and CRLF differences (Linux/Windows compatibility).
- [ ] **Automated Stress Testing**: Built-in generator and brute-force comparer.
- [ ] **100% Offline**: Zero network requests, fully compliant with exam rules.
- [ ] **CLI-first architecture**: UI will be completely decoupled.

## 🛠️ Tech Stack
- **Core**: Pure C++ (C++17)
- **Architecture**: Interface-Driven Development (Pure virtual interfaces `.hpp`).
- **Platform API**: Windows API (`<windows.h>`), with future Linux support (`fork`, `exec`).

## 📦 Project Structure
```text
OI-Core/
├── CMakeLists.txt      # Build system
├── include/            # Core Abstract Interfaces (For frontend & 2nd-dev)
│   ├── common/         # Shared data structures (Enums, Result structs)
│   └── backend/        # Pure virtual interfaces (IBackend, ISandbox, ICompiler)
├── src/                # Implementation details
│   ├── core/           # Business logic implementations
│   └── platform/       # OS-specific system calls (e.g., Windows Sandbox)
└── tests/              # Test cases
```

## 🚀 Quick Start
*(Note: Make sure you have CMake and a C++ compiler installed)*

```bash
# Clone the repository
git clone https://github.com/whdxh/OI-Core.git
cd OI-Core

# Build the project
mkdir build && cd build
cmake ..
make # Or use cmake --build . on Windows
```

## 📄 License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
