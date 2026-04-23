# Docker C Multistage Build

> Compile a C program inside Docker using a **multistage build** — the builder stage compiles the binary, and the final image contains only the executable. Result: a tiny, production-ready image.

---

## Project Structure

```
docker-c-multistage/
├── hello.c        # C source program
└── Dockerfile     # Multistage build definition
```

---

## Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed and running
- Basic familiarity with the terminal

---

## Getting Started

### Step 1 — Create the project folder

```bash
mkdir docker-c-multistage
cd docker-c-multistage
```

---

### Step 2 — Write the C program

Create and edit `hello.c`:

```bash
touch hello.c
nano hello.c
```

Add the following code:

```c
#include <stdio.h>

int main() {
    printf("Hello from C program inside Docker!\n");
    return 0;
}
```

---

### Step 3 — Write the Dockerfile

Create and edit `Dockerfile`:

```bash
touch Dockerfile
nano Dockerfile
```

Add the following multistage build definition:

```dockerfile
# ── Stage 1: Builder ──────────────────────────────────────────
FROM ubuntu:22.04 AS builder

RUN apt-get update && apt-get install -y gcc

COPY hello.c .

RUN gcc -static -o hello hello.c

# ── Stage 2: Minimal Runtime ──────────────────────────────────
FROM scratch

COPY --from=builder /hello /hello

CMD ["/hello"]
```

> **Why two stages?**
> - **Stage 1 (builder):** Uses Ubuntu + GCC to compile the C source into a static binary.
> - **Stage 2 (scratch):** Starts from an empty image and copies only the compiled binary — no OS, no compiler, no bloat.

---

### Step 4 — Verify project files

```bash
ls
```

```
Dockerfile    hello.c
```

---

### Step 5 — Build the image

```bash
docker build -t cprogramminimal .
```

**What happens during the build:**

| Stage | Step | Description |
|-------|------|-------------|
| builder | 1/4 | Pull `ubuntu:22.04` base image |
| builder | 2/4 | Run `apt-get install gcc` |
| builder | 3/4 | Copy `hello.c` into the builder |
| builder | 4/4 | Compile with `gcc -static -o hello hello.c` |
| stage-1 | 1/1 | Copy only the binary into the final image |

> Build time: ~0.8s (after cache). First run pulls Ubuntu and installs GCC.

---

### Step 6 — Check the image was created

```bash
docker images
```

You should see your image listed:

```
REPOSITORY        TAG       IMAGE ID       DISK USAGE
cprogramminimal   latest    6cbb35d17efb   968kB
```

> Only **968 kB** — because the final image contains nothing but the compiled binary!

---

### Step 7 — Run the container

```bash
docker run -it cprogramminimal
```

---

## Expected Output

```
Hello from C program inside Docker!
```

---

## How Multistage Builds Work

```
hello.c  ──►  [Ubuntu + GCC]  ──►  hello (binary)  ──►  [scratch image]
              Stage 1: builder                           Stage 2: final
```

- The **builder** stage has Ubuntu and GCC — everything needed to compile.
- The **final** stage starts from `scratch` (completely empty) and only receives the compiled binary.
- This keeps the final image **tiny and secure** — no compiler, no OS packages, no attack surface.

---

## Image Size Comparison

| Image | Contents | Size |
|-------|----------|------|
| `ubuntu:22.04` | Full OS | ~108 MB |
| `cprogramminimal` | Binary only | **~968 kB** |

---




<img width="1467" height="29" alt="Screenshot 2026-04-23 at 12 50 23 PM" src="https://github.com/user-attachments/assets/bc4c39bd-d06a-4480-8067-7b34ed1f398d" />


<img width="1468" height="870" alt="Screenshot 2026-04-23 at 12 50 07 PM" src="https://github.com/user-attachments/assets/acf479b3-17fb-4d7e-b2b0-9a9a0dd63439" />


<img width="1469" height="323" alt="Screenshot 2026-04-23 at 12 49 48 PM" src="https://github.com/user-attachments/assets/1d9e02a7-7cc5-4553-a170-ce74dfb196f1" />


<img width="1469" height="83" alt="Screenshot 2026-04-23 at 12 49 36 PM" src="https://github.com/user-attachments/assets/d60e0135-cd78-423d-95c5-f7b2f1f04631" />


<img width="1470" height="46" alt="Screenshot 2026-04-23 at 12 49 26 PM" src="https://github.com/user-attachments/assets/585a9747-0e03-40ea-b3cd-d1aa0f791caf" />








