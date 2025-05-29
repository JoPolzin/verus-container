# `verus-container`

**Dockerfiles for Verus**, the verification tool built on Rust.
Both images are based on `rust:1.82.0-slim`, which means they're running Debian under the hood and use the standard Rust toolchain.

This repository provides two container setups:

---

## 🐳 Available Dockerfiles

### 🔹 `Dockerfile.bin`

Fetches the **latest binary release** of Verus from GitHub.
Use this if you just want to *run* Verus without compiling it.

### 🔹 `Dockerfile.source`

Builds Verus from the **latest commit** on the `main` branch.
Use this if you want to build from source, test latest changes, or patch Verus.
You can also modify the Dockerfile to check out a specific commit or tag, and adjust the Rust version used.

> **Note:** Verus relies on a specific version of Rust. When modifying the Dockerfile to use another version, make sure to also update the `FROM rust:<version>-slim` line accordingly.
---
## Obtaining the Containers:
Obtain the Dockerfiles by running 
```bash
git clone git@github.com:JoPolzin/verus-container.git
```
Optionally, you can directly pull the containers by running: 
```bash
docker pull ghcr.io/jopolzin/verus-container:source
docker pull ghcr.io/jopolzin/verus-container:latest
```
or with podman:
```bash
podman pull ghcr.io/jopolzin/verus-container:source
podman pull ghcr.io/jopolzin/verus-container:latest
```
When using this option you can skip the next step of building the container

---

## 🧪 Building the Containers

You can build the images using either **Podman** or **Docker**.

### With **Podman**:

```bash
podman build -f Dockerfile.bin -t verus_bin .
```

* `-f Dockerfile.bin` tells Podman which Dockerfile to use.
* `-t verus_bin` tags the resulting image with the name `verus_bin`.

```bash
podman build -f Dockerfile.source -t verus_source .
```

* This builds from source instead, using the source-based Dockerfile.

### With **Docker**:

```bash
docker build -f Dockerfile.bin -t verus_bin .
docker build -f Dockerfile.source -t verus_source .
```

* Commands are identical to Podman, just replace `podman` with `docker`.

---

## 🚀 Running the Container

> If you're using Docker instead of Podman, just replace `podman` with `docker` in all commands below.

### Run the container:

```bash
podman run --rm -it verus_source
```

* `--rm`: Automatically remove the container when it exits.
* `-it`: Enables interactive mode (useful if the program prompts or outputs to terminal).

### Mount a project into the container:

```bash
podman run --rm -v "{PATH_TO_YOUR_PROJECT}:{PATH_IN_THE_CONTAINER}" verus_source
```

* `-v`: Mounts a directory from the host into the container.
* Replace `{PATH_TO_YOUR_PROJECT}` with the absolute path to your local code.
* Replace `{PATH_IN_THE_CONTAINER}` with where you want it to appear in the container (e.g., `/work`).

---

## ⚙️ Running Verus on a File

If you want to run Verus on a file in your current working directory:

```bash
podman run --rm -v "$(pwd):/work" -w /work verus_bin verus main.rs
```

* `$(pwd)`: Gets the current working directory.
* `-v "$(pwd):/work"`: Mounts the current directory into `/work` in the container.
* `-w /work`: Sets the working directory inside the container to `/work`.
* `verus main.rs`: Runs Verus on the mounted file `main.rs`.

You can do the same with the source-built image:

```bash
podman run --rm -v "$(pwd):/work" -w /work verus_source verus main.rs
```

---

### Developing Inside the Container

To interact with the container directly (e.g., for debugging, trying commands):

```bash
podman run --rm -it -v "$(pwd):/work" -w /work verus_source
```

This runs the container your project folder mounted inside.

From inside the container you can then run verus with your desired arguments and options:
```bash
verus ./main.rs
```

> ⚠️ **Reminder:** Any files created or modified *inside* the container are lost when it exits—unless you mounted a volume. Always save your work!

---

## ⚠️ Rust Version Compatibility

When targeting a specific Verus version, make sure to match the expected Rust version.
Verus is typically built and tested against a specific Rust release. To avoid incompatibilities, update the Dockerfile line like so:

```Dockerfile
FROM rust:<expected-version>-slim
```

Example:

```Dockerfile
FROM rust:1.51.0-slim
```

You can check which Rust version is needed by looking at the [`rust-toolchain`](https://github.com/verus-lang/verus/blob/main/rust-toolchain) file in the Verus repository.
