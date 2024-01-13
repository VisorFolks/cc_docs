---
tags:
  - tools
  - links
  - "#wsl"
  - vscode
---
# 2.0 Pre-requisites and Links
To get started with Cyancore on your development machine, here are few things user needs to do or user may need.

- Linux machine or Windows with wsl enabled.
  - Click [here for WSL](https://docs.microsoft.com/en-us/windows/wsl/install) installation process.
- User can either use [VS-Code](https://code.visualstudio.com/) (remote) or [vim](https://www.vim.org/)/[nano](https://www.nano-editor.org/)/etc. editors for making changes.
- Install dependencies, run following command: (Ubuntu)
    ```shell
    sudo apt update && sudo apt install build-essential cppcheck tree ninja-build libpixman-1-dev libcairo2-dev libpango1.0-dev libjpeg8-dev libgif-dev libglib2.0-dev libgcrypt20-dev -y
    ```
    _These are necessary to fetch tools like make, native gcc, linting tools and other libraries needed by other tools such as qemu._
- Knowledge of using git is good to have.
- [_Optional but Recommended_] **Add ssh key to github**, click [here for guide](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account). This will let user access github without username and password. However, please do [setup git cli/gui and configure your system account](https://docs.github.com/en/get-started/quickstart/set-up-git) on your workstation.
- Run the following command to download/clone Cyancore
	```shell
	git clone git@github.com:VisorFolks/cyancore.git
	```

After cloning is complete, user can now play around with Cyancore.
For quick help, run:
```shell
make help
```

## 2.1 Programming Languages/Scripts
1. C/C++
2. Assembly (asm)
3. makefile
4. Linker Script

## 2.2 Tools
All the tools that we use are opensource and non-proprietary. Below are few useful links

#### 2.2.1 GCC
- [gcc Online user guide](https://gcc.gnu.org/onlinedocs/) #link_gcc
- [gcc inline assembly user guide](https://gcc.gnu.org/onlinedocs/gcc/Using-Assembly-Language-with-C.html) #link_asm

#### 2.2.2 Linker
- [Linker Script guide](https://ftp.gnu.org/old-gnu/Manuals/ld-2.9.1/html_chapter/ld_3.html) #link_linker

#### 2.2.3 GNU make
- [makefile user guide](https://www.gnu.org/software/make/manual/make.html) #link_make 

#### 2.2.4 git
- [git user guide](https://git-scm.com/docs/user-manual.html) #link_git

#### 2.2.5 QEMU
- [Webpage](https://www.qemu.org/) #link_qemu
- [Repository](https://gitlab.com/qemu-project/qemu)
#### 2.2.6 VisorFolks Tools
- [Repository](https://github.com/orgs/VisorFolks/repositories)

<< [[Chapter 1 - Hello World!]]
\>> [[Chapter 3 - Abbreviations and terms in CC]]