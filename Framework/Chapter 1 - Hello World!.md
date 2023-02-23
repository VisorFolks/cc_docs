# 1.0 Hello World!
tags: #introduction

*Hello World from VisforFolks! Welcome to an open-source unified software platform for embedded systems projects!*

### 1.1 Inception of Cyancore
Cyancore got started as hobby project with a motivation to write a portable embedded system software projects which can run on any of the hardware platforms irrespective of the platform configuration.

Cyancore platform's first commit was made on **_5th Sept 2019_**. It was initially called as Arc (branch: arc_old), later renamed to Cyancore. When Arc was under development, framework's architecture was inspired from that of Linux Kernel's. But as the development progressed, many setbacks and limitations were discovered which are getting addressed as the development progresses. For instance, support for multiple software layers is provisioned, replacement of dts files with libresource, etc.

For platforms comprising of multiple subsystems like cpu, gpu, memory, radio, a/v, io, etc., need multiple software stacks which are developed by various teams, each having exclusive standards. This can be one of the reasons for failures while integrating the whole system. Thus, arises need for a framework that houses support for various cpus, hardware engines, modules, platforms, etc. While developing Cyancore, this very feature was given importance. As Cyancore provides a clear demarcation between various cpu archs (inter/intra), platforms and software layers it easy to develop desired software layer.

### 1.2 Why Cyancore
Cyancore's key component for features it delivers, is the build system. Cyancore uses GNU makefile based build engine. The whole build engine is accumulation of various make modules which lets developer build a robust framework that can address various requirements from addition of a build target to linking various projects build in daisy-chain fashion. Because of such modularity, creation of libraries and other binaries is extremely easy. This very feature allows developer to design a custom solution tailored for a platform without compromising on software optimization, speed, and yet having a unified approach.

Although it is a comprehensive framework, it also let newbies to get started with simple projects. It provides opportunity to understand the inner working of cpus/mcus and offers flexibility to port their projects on any hardware/platform architecture without much of a hassle. Because of unification and relatively tighter integration of features and abilities, Cyancore can utilize the hardware and software resources better and improve various KPIs of the final product.

With highly robust build engine, well defined abstractions in software layers and modules it lets the developer, or a team build complex project(s) by re-using the existing project's sources and by changing flags in config.mk.

### 1.3 For whom it's meant
This is a framework by all for all... Students, teachers, hobbyists, corporate, professionals, freelancers, etc. Though this framework supports various hardware platforms and software modules, we aim to keep it simple, intuitive and well structured so that every user can benefit from using this and enjoy the features of this framework.

> This doesn't mean user/developer can skip reading documentation of platform/cpu and Cyancore code. It is good practice to first read the documentation of the hardware platform and Cyancore code to avoid surprises later during project development

Cyancore is indented to be used as tool for learning, developing, creating and exploring the world of embedded systems.

[[Chapter 2 - Pre-requisites and Links]]