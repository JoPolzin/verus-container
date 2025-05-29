# `verus-container`

**Dockerfiles for Verus**, the verification tool built on Rust.
Both images are based on `rust:1.82.0-slim`, which means they're running Debian under the hood.

This repository provides two container setups:

---

## 🐳 Available Dockerfiles

### 🔹 `Dockerfile.bin`

Fetches the **latest binary release** of Verus from GitHub.

### 🔹 `Dockerfile.source`

Builds Verus from the **latest commit** on `main`.
You can tweak this file to build a specific version by modifying the Git clone step and the base Rust version (`FROM rust:<version>-slim`), since Verus requires a specific Rust toolchain.

---

## 🧪 Building the Containers

### With **Podman**:

```bash
podman build -f Dockerfile.bin -t verus_bin .
podman build -f Dockerfile.source -t verus_source .
```

### With **Docker**:

```bash
docker build -f Dockerfile.bin -t verus_bin .
docker build -f Dockerfile.source -t verus_source .
```

---

## 🚀 Running the Container

Example run using a volume mount:

```bash
podman run --rm -v "$(pwd):/work" -w /work verus_source verus ./your_file.rs
```

Or if you built the binary-based image:

```bash
docker run --rm -v "$(pwd):/work" -w /work verus_bin verus ./your_file.rs
```

---
## ⚙️ Running Verus

From inside the container run:
```bash
verus --version
```

---

## ⚠️ Rust Version Compatibility

When targeting a specific Verus version, **you must also match the expected Rust version**.
Update the base image line in the Dockerfile accordingly:

```Dockerfile
FROM rust:<expected-version>-slim
```
