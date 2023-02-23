# 4.0 GNU makefile base build engine
tags: #build #make #build_vars

## 4.1 GNU make introduction
GNU Make is a tool which controls the generation of executables and other non-source files of a program from the program's source files.

Make gets its knowledge of how to build your program from a file called the _makefile_, which lists each of the non-source files and how to compute it from other files. When you write a program, you should write a makefile for it, so that it is possible to use Make to build and install the program.
[Credits: gnu.org Make]

## 4.2 Design of make build engine in CC
Cyancore framework only uses make based build engine. This decision was taken to keep things simple and compatible with legacy and upcoming projects.
Cyancore's build engine is fully modularized and split into independent targets which can be assigned dynamically.

```shell
$ tree mk --charset=ascii
mk
|-- copy_to_remote.mk
|-- elf.mk
|-- lib.mk
|-- lint.mk
|-- lobj.mk
|-- mk_helper.mk
|-- obj.mk
|-- path.mk
|-- project.mk
|-- slib.mk
|-- tc.mk
\-- tc_get.mk
```

In CC's root folder, all the make modules are located under `mk/` folder. However, the build uses `makefile` located in the root of CC's folder. The build engine utilizes all the cores of `Host` machine to speed up the build process.
> N_JOBS is the variable that store the n(host_cores)

## 4.3 Key Variables of the build engine in CC
- `V` - Verbose, to be used as `$ make <project> V=1` enables verbose build logs. Default V = 0
- `PP` - PreProcess, to be used as `$ make <project> PP=1` enables preprocessing of source files. Default PP = 0
	> _TFD_: To check the preprocess C file which is under dev, use `$ make <project> PP=1 <file>.o`
- `N_JOBS` - Number of make Jobs to be launched as **default** target. This variable holds the number of cpus/cores the host machine has, it used to spawn make threads/jobs to speed up build process. Default value of N_JOBS = 8 in octa core machine or N_JOBS = 4 in quad core. This value can be overwritten by `$ make <project> N_JOBS=x`, where x = 1, 2, 3...
- `CC_ROOT` - Holds the path of CC root
- `TOOLS_ROOT` - Holds the path of TOOLS_ROOT, this variable can be passed via cmd e.g.: `$ make <project> TOOLS_ROOT = ~/tools`
- `MISC_TOOLS` - Holds the path of misc. tools which will be inside TOOLS_ROOT. This path is not intended to be modified as it used by build engine.
- `SRC` - Holds the path for sources of CC.
- `OUT_PATH` - Holds the path for the all build output. This can be modified via cmd e.g.: `$ make <project> OUT_PATH = ~/build`
- `OUT` - Holds the path for the project build output. This can be modified as shown above with "OUT" variable. However, it is recommended this should not be modified. Its best to modify OUT_PATH.
- `GET_PATH` - Holds the path of current makefile. For example, if the build.mk is located in a/b/c/build.mk, GET_PATH will have "a/b/c". This variable (**readonly**) gets allocated during runtime and should **never** be modified anywhere in the makefiles.
- `P_TARGETS` - Project dependent Targets. This variable holds all the targets that needs project name to execute the commands. This variable needs to be updated if new project dependent target is added to build. For example, `temp` is new target that depends on project, then `P_TARGETS += temp` needs to be performed else the build engine will not be able to identity the dependency of project.
- `T_ALLOWLIST` - Targets Allowed which are not dependent on project name. For example, `$ make help`, help is independent of the project name. This variable is necessary as it separates out project name for targets and assigns the `PROJECT` variable accordingly.
	> _TFD_: Project names should be same as target name.
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

<< [[Chapter 3 - Abbreviations and terms in CC]]
\>> [[Chapter 5 - Understanding CC src folder]]