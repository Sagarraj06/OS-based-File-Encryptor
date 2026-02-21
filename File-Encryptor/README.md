# 🔐 OS-Based File Encryptor

A C++17 file encryption engine that recursively discovers all files in a target directory and applies an in-place **Caesar-cipher byte transformation** for encryption and decryption.

---

## 📁 Project Structure

```
File-Encryptor/
├── main.cpp                          # Entry point — directory traversal & task dispatch
├── Makefile                          # Builds two binaries: encrypt_decrypt & cryption
├── .env                              # Stores the Caesar cipher shift key
└── src/app/
    ├── fileHandling/
    │   ├── IO.hpp / IO.cpp           # Binary file stream management
    │   └── ReadEnv.cpp               # Reads shift key from .env
    ├── processes/
    │   ├── Task.hpp                  # Task struct (filepath + action + fstream)
    │   ├── ProcessManagement.hpp
    │   └── ProcessManagement.cpp     # std::queue-backed task pipeline
    └── encryptDecrypt/
        ├── Cryption.hpp / Cryption.cpp   # Caesar cipher (encrypt/decrypt)
        └── CryptionMain.cpp              # Standalone cryption worker binary
```

---

## ⚙️ How It Works

1. **Directory Traversal** — `std::filesystem::recursive_directory_iterator` walks the target directory and finds all regular files.
2. **Task Creation** — Each file is wrapped into a `Task` object holding its path, action (`ENCRYPT`/`DECRYPT`), and an open `std::fstream`.
3. **Queue Pipeline** — Tasks are pushed into a `std::queue` managed by `ProcessManagement`.
4. **In-place Cipher** — Each byte is shifted using Caesar cipher (`(ch + key) % 256` for encrypt, `(ch - key + 256) % 256` for decrypt) directly on the file using `seekp` + `put`.
5. **Dual-Binary Design** — A separate `cryption` binary can receive a serialized task as a CLI argument and process it independently, enabling future `fork`/`execv`-based parallelism.

---

## 🚀 Build & Run

### Prerequisites
- macOS / Linux
- `g++` with C++17 support

### Build
```bash
make
```
This produces two binaries:
- `encrypt_decrypt` — main orchestrator
- `cryption` — standalone worker

### Run
```bash
./encrypt_decrypt
```
```
Enter the directory path: /path/to/your/files
Enter the action (encrypt/decrypt): encrypt
```

### Configure Shift Key
Edit the `.env` file with any integer shift value:
```
7
```

### Clean
```bash
make clean
```

---

## 🛠️ Tech Stack

| Technology | Usage |
|---|---|
| C++17 | Core language |
| `std::filesystem` | Recursive file discovery |
| `std::fstream` | Binary in-place file I/O |
| `std::queue` | Task pipeline |
| Caesar cipher | Byte-level encryption/decryption |
| `fork` / `execv` | Per-file process parallelism (scaffolded) |

---


