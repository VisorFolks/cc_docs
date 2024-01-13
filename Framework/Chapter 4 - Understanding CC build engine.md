---
tags:
  - build
  - make
  - build_vars
  - maitri
---
# 4.0 Maitri, a GNU makefile base build engine
The build engine is the key component of Cyancore which deeply contributes to it's unique quality of scalability and dynamic builds. Because of such supporting nature of this simple yet modular build engine, we have named it as **Maitri**.
> _**Samskrutam**/Sanskrit_: Maitri means a friend, a support.

_Maitri_ is a gnu make based build engine which take advantage of make's unique nature such as multi target execution, secondexpansion, target dependency, wildcards, shell scripting, pattern based dynamic targets, etc.
#### 4.0.1 GNU make introduction
GNU Make is a tool which controls the generation of executables and other non-source files of a program from the program's source files.

Make gets its knowledge of how to build your program from a file called the _Makefile_, which lists each of the non-source files and how to compute it from other files. When you write a program, you should write a makefile for it, so that it is possible to use Make to build and install the program.
[Credits: gnu.org Make #link_make]

## 4.1 Usage of Maitri
We are motivated to make "Maitri" more intuitive to users. Using Maitri, user can build a project just by passing project name as target of makefile. Engine of Maitri can automatically recognize the name of project and accept additional args. For example:
```shell
make demo_avr # builds project demo_avr with default args
```

> The build engine can accept multiple targets. However, while issuing project based targets, the engine expects only one project name as first target and following that will be it's dependent targets.

Below is the usage template:
```shell
make <Project Name> <Targets> <Build Variables>
```
#### 4.1.1 Targets
This section details about the build targets and it's dependency on project name.

Project independent targets supported on Maitri are as follows:
- Generic:
	- `help` - prints build engine help
	- `list` - prints available projects
	- `clean` - cleans all build artifacts
	- `all_projects` - builds all projects
- Toolchains:
	- `get_all_tc` - fetches toolchain for all the supported CPU archs
	- `get_avr_tc` - fetches toolchain for AVR MCUs
	- `get_arm_tc` - fetches toolchain for ARM Cores
	- `get_riscv_tc` - fetched toolchain for RISC-V Cores
- Emulator tools:
	- `get_qemu` - fetches qemu and performs builds locally. Ignore this if qemu is already installed
	- `clean_qemu` - removes local qemu installation
	- `qemu_test` - builds project demo_qemu and starts qemu
- Raspberry Pi picotools:
	- `install_pt_dep` - installs picotool dependencies
	- `get_picotool` - fetches picotool sources and builds locally
	- `clean_picotool` - removes local picotool installation

Project dependent targets supported on Maitri are as follows:
- Build:
	- `default` - this is default target of makefile, it need not be provided in cmd line args. In Maitri, default perform build using `N_JOBS` [#njobs] cores with target as `cyancore`
	- `cyancore` - builds cyancore based project
	- `clean` - removes "Project Name" build artifacts only. This is a target that can remove all the build artifacts which project name is not specified
- Raspberry Pi picotools:
	- `elf2uf2` - generates uf2 image from ARM-ELF file
- Remote workspace tools:
	- `copy_to_remote` - send binaries to remote workspace for debug or flashing purpose
	- `clean_remote` - removes binaries from remote workspace
- Miscellaneous tools:
	- `check` - performs static analysis/lint using cppcheck tool
#### 4.1.2 Build Variables
To assist build and developer to perform their tasks, Maitri provides few build variables that can be set through command like. Following are the list of few variables that can be provided:
- `V` - verbose make command / build logs. Default value is `0`. To enable build logs, set it to `1`
- `PP` - preprocess source files. Default value is `0`. To generate preprocessed files, set it to `1`
- `DEBUG` - sets debug level. Valid values are 0, 1, 2, 3, 4, 5.
	- Each debug level is associated with dept of information that needs to be printed during run time while performing debug
	- For more information on debug level refer to #libsyslog
	- If DEBUG is set to 0 (Default value), compiler will not generate debug symbols on binary. It's as good as release build
- `N_JOBS` - number of host core on which make threads can be deployed. Default value is Number of core on the workstation. _Unless necessary, avoid passing this via args_
#### 4.1.3 Usage Illustrations
To get started with Maitri (makefile), below commands prints help
```shell
make help
```
To proceed forward with this section, lets take "demo_avr" project. First, let's fetch toolchain for AVR cores
```shell
make get_avr_tc
```
To get list of projects available for build
```shell
make list
```
To perform regular build of "demo_avr"
```shell
make demo_avr
or
make demo_avr default
```
To perform single threaded build of "demo_avr"
```shell
make demo_avr cyancore
or
make demo_avr N_JOBS=1 # not recommended to use
```
To perform verbose build
```shell
make demo_avr V=1
```
To generate preprocessed file
```shell
make demo_avr PP=1
```
To perform verbose build and generate preprocessed file
```shell
make demo_avr V=1 PP=1
```
To clean workspace, this removes **all** built files
```shell
make clean
```
To clean demo_avr built files
```shell
make demo_avr clean
```
To enable debug on a project
```shell
make demo_avr DEBUG=2 # setting debug level to 2
```
To perform clean build
```shell
make demo_avr clean default
```
To start qemu_test
```shell
make qemu_test
```
To perform check on project
```shell 
make demo_avr check
```
##### 4.1.3.1 Special use case for developers [TFD]
To build a source file or library, developer can use Maitri to do the same effortlessly. To compile only one file
```shell
make demo_avr project.o
```
Upon executing the above command, under the out (build) folder, only project.c file will be compiled and project.o file will be genrated. If the developer wishes to preprocess and verify the build artifact
```shell
make demo_avr project.o PP=1
```
a new file project.pre.c will be generated and deposited under relevant build folder. Similarly to build a library
```shell
make demo_avr libc.a
```

## 4.2 Design of Maitri
Cyancore framework only uses make based build engine. This decision was taken to keep things simple and compatible with legacy and upcoming projects. Maitri is fully modularized make based build with advantages as listed below:
- "make" is widely using across industries, this makes it easy to use with almost no dependencies.
- Extremely modular which support 2 types of targets, project dependent and project independent. This helps developer add their make modules easily with dependency resolution.
- Supports daisy chaining of project builds by addition of auxiliary targets `aux_target`.
- Supports single/multi thread build by passing targets.
- Supports creation of pre-processed C files for debug.
- Supports building of 1 object file with all relevant flags on demand.
- Easy to scale.
- Easy to use by just including 1 mk file module.

The Maitri engine consists of below files: (_these might change with time_)
```shell
$ tree mk --charset=ascii
mk
|-- copy_to_remote.mk
|-- elf.mk
|-- help.mk
|-- lib.mk
|-- lint.mk
|-- lobj.mk
|-- mk_helper.mk
|-- obj.mk
|-- path.mk
|-- picotool.mk
|-- project.mk
|-- qemu.mk
|-- slib.mk
|-- tc.mk
\-- tc_get.mk
```

In CC's root folder, all the make modules are located under `mk/` folder. However, the build uses `makefile`, which located in the root of CC's folder. The build engine utilizes all the cores of `Host` machine to speed up the build process.

## 4.3 Key Variables of the build engine in CC
- `V` - Verbose, enables verbose build logs. When `V=1`, a special targte `.SILENT` is enabled which disables echoing.
- `PP` - Preprocess, this variable enables generation of preprocessed c or asm files.
- `N_JOBS` - Number of make Jobs to be launched as **default** target. This variable holds the number of cpus/cores the host machine has, it used to spawn make threads/jobs to speed up build process. Default value of N_JOBS = 8 in octa core machine or N_JOBS = 4 in quad core. [#njobs]
- `CC_ROOT` - Holds the path of CC root
- `TOOLS_ROOT` - Holds the path of TOOLS_ROOT, this variable can be passed via cmd e.g.: `$ make <project> TOOLS_ROOT = ~/tools`
- `MISC_TOOLS` - Holds the path of misc. tools which will be inside TOOLS_ROOT. This path is not intended to be modified as it used by build engine.
- `SRC` - Holds the path for sources of CC.
- `OUT_PATH` - Holds the path for the all build output. This can be modified via cmd e.g.: `$ make <project> OUT_PATH = ~/build`
- `OUT` - Holds the path for the project build output. This can be modified as shown above with "OUT" variable. However, it is recommended this should not be modified. Its best to modify OUT_PATH.
- `GET_PATH` - Holds the path of current makefile. For example, if the build.mk is located in a/b/c/build.mk, GET_PATH will have "a/b/c". This variable (**readonly**) gets allocated during runtime and should **never** be modified anywhere in the makefiles.
- `P_TARGETS` - Project dependent Targets. This variable holds all the targets that needs project name to execute the commands. This variable needs to be updated if new project dependent target is added to build. For example, `temp` is new target that depends on project, then `P_TARGETS += temp` needs to be performed else the build engine will not be able to identity the dependency of project.
- `T_ALLOWLIST` - Targets Allowed which are not dependent on project name. For example, `$ make help`, help is independent of the project name. This variable is necessary as it separates out project name for targets and assigns the `PROJECT` variable accordingly.
>_TFD_: Project names should be same as target name.
- `PROJECT` - Holds the name of project. When passed through cmd,
	`$ make default PROJECT=<project>`
	project name and command extraction routine will be bypassed and executes build. 
- `CMD` - Command/Traget for the project. It's determined by the build engine.
- `ELF` - Name of final build binary. It's dynamically programmed to name of `<project>.elf`
- `LD_SCRIPT` - Holds the full path of linker script file.
- `LD_SUPPLEMENT` - Holds the full path of the supplementary linker script. These are like extension of the `LD_SCRIPT` which provide information about custom memory sections.
- `C_SRCS` - Holds the path for C source file. This variable is allocated dynamically in runtime. This variable is used by the `gcc` compiler.
- `S_SRCS` - Holds the path for ASM/S source file. This variable is allocated dynamically during runtime. This variable is used by `as` assembler.
- `CPP_SRCS` - Holds the path for CPP source file. This variable is allocated dynamically during runtime. This variable is used by `g++` compiler.
- `DEP_SRCS` - Holds the path for all source files. This variable is allocated dynamically during runtime. This variable is used by linting tool and other misc. targets.
- `C_OBJS` - Holds the path for C object file (output). This variable is allocated dynamically during runtime.
- `NO_LOGS` - Disables logging and eliminates codes which prints logs. Use only if memory constraints are present.
>The make VARIABLES mentioned below are located in src/engine/banner.mk
- `NAME` - Codename of the release.
- `VERSION` - Version in hex. Consider VERSION=0xabcdefgh, then:
	- "ab" corresponds to change in architectures, it will increment only if there is major CPU related change such as addition of CPU support.
	- "cdef" corresponds to major change in framework. If "ab" changes, the cdef will start from 0 and it will increment if framework goes thru major change without major changes in CPU architecture. Major changes include, addition on new drivers, features, functionality, non CPU architectural changes, changes in data flow and designs.
	- "gh" corresponds to minor change in framework, such as bug fixes, documentation, cosmetic changes, cleanup and code restructuring. if "cdef" reset to 0, so will "gh" and will increase upon previously listed changes.
	- This changes will only be made by Administrators of code base.

<< [[Chapter 3 - Abbreviations and terms in CC]]
\>> [[Chapter 5 - Understanding CC src folder]]