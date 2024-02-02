# OpenCyphal + Zephyr: example project

In this project I will show how one can setup an Zephyr project for a simple OpenCyphal device: a remotely controlled LED.

I'll be using the following:

- OS: Ubuntu 22.04
- Board: [Nucleo F756-ZG](https://www.st.com/en/evaluation-tools/nucleo-f756zg.html)
- Zephyr: [maksimdrachov/zephyr](https://github.com/maksimdrachov/zephyr/tree/opencyphal-zephyr) (`opencyphal-zephyr` branch)
  - I recommend doing the same (make fork, create branch) for the following reasons:
    - Sometimes it might be necessary to make small changes in the Zephyr codebase (one should strive to keep those as minimal as possible though).
    - By using a seperate branch you can update main and rebase (if some update is necessary).

## 1. Setup

To get started we'll need to a bunch of setup steps, this part heavily borrows from [maksimdrachov/zephyr-rtos-template](https://github.com/maksimdrachov/zephyr-rtos-template), so I won't be explaining every step here (check there if you're not sure).

For now I'll just assume you have done the following steps:

```bash
# Setup repository
cd ~
mkdir opencyphal-zephyr
cd opencyphal-zephyr
git init
git remote add origin git@github.com:maksimdrachov/zephyr-rtos-template.git

# Create .west
mkdir .west
cd .west
touch config
```

`.west/config`:

```yml
[manifest]
path = zephyr
file = west.yml
```

```bash
cd ~/opencyphal-zephyr
git submodule add https://github.com/maksimdrachov/zephyr zephyr # We will keep track of branch/commit through .git
west update # This will take a while, get some coffee
```

<details>
<summary>Copy this to `.gitignore`</summary>

```
# Prerequisites
*.d

# Compiled Object files
*.slo
*.lo
*.o
*.obj

# Precompiled Headers
*.gch
*.pch

# Compiled Dynamic libraries
*.so
*.dylib
*.dll

# Fortran module files
*.mod
*.smod

# Compiled Static libraries
*.lai
*.la
*.a
*.lib

# Executables
*.exe
*.out
*.app

# macOS
.DS_Store

# Zephyr
bootloader/
/modules/
tools/
build/
zephyr-project-template/build/
twister-out*/

# .vscode
.vscode/.cortex-debug*

# Python
venv/
__pycache__/

# scripts
scripts/platform-tests-results/
*.bin

# nox
.nox/
.pytest_cache/
.coverage*

# SonarCloud
.scannerwork/
```
</details>

Now let's run a simple blinky to check if our toolchain is setup correctly:

```bash
cd ~/opencyphal-zephyr
west build -b nucleo_f756zg ./zephyr/samples/basic/blinky
west flash # Should start blinking
```

## 2. Minimal template for application

Now let's create a minimal application (we will come back to this later).

```bash
cd ~/opencyphal-zephyr
mkdir app
```



