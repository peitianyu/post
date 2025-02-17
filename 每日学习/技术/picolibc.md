---
title: picolibc
date: 2025-2-17 12:30:12
categories:
  - 技术
tags:
  - 嵌入式
---

Picolibc 是一种适用于小型微控制器嵌入式系统设计的轻量级C标准库，该库的API甚至允许在低内存 (RAM) 设备中运行。它其实是“newlib-nano”的升级版本，这个升级版本有一些有趣的变化，比如：用从avrlibc采用 ATMEL 特定的 printf 代码替换“stdio”库。它于2019年9月下旬由知名开发者 Keith Packard通过其博客宣布推出；作为这个库的一部分，Keith 还推出了 picocrt，它负责在调用自己的 C 程序主函数之前初始化内存和调用各种构造函数。采用混合的BSD协议授权。它兼具高效性和完整性，是嵌入式开发者的理想选择。


PicoLibC is a Lightweight C library for Embedded Systems.

Picolibc is library offering standard C library APIs that targets small embedded systems with limited RAM. Picolibc was formed by blending code from Newlib and AVR Libc.

Picolibc是一个提供标准C库API的库，目标是那些RAM有限的小型嵌入式系统。它通过融合Newlib和AVR Libc的代码而形成。这意味着Picolibc继承了这两个优秀项目的优点，并进行了精心的优化，使其更加适合资源受限的环境。它不仅体积小巧，而且功能完善，支持众多常用的C库函数，例如printf、scanf、malloc、free等，能够满足大多数嵌入式应用的需求。

特点
    是 newlibc 的修订版，它没有成熟的 stdio lib，使用的是avrlibc 的轻量级 stdio lib，更适合低内存嵌入式设备。
    Meson 构建系统简化了针对各种目标平台和硬件 picolibc 源代码树的构建过程。
    使用 Glibc 作为参考更新了数学测试套件。
    该库是 BSD 许可的，非 BSD 组件已从源代码树中删除。
    picocrt-picolibc 启动代码，无需编写自己的代码即可初始化嵌入式系统，这可以通过在编译时使用-specs= picolibc.specs来启用。
    使用内置的线程本地存储 (TLS)，它有几个优点：
        源代码更简单，因为线程局部变量可以通过名称直接访问；
        线程本地存储仅包含应用程序使用的值；
        生成的代码更小更快。


核心优势

    1.极致轻量级: Picolibc 的设计目标是尽可能减小库的大小，从而最大限度地节省嵌入式系统的内存资源。这对于内存资源紧张的微控制器和嵌入式系统来说至关重要。

    2.高效性能: Picolibc经过精心的优化，在保证功能完整性的同时，最大限度地提高了运行效率。它在代码编写上注重简洁性与效率，避免了不必要的开销。

    3.广泛的架构支持: Picolibc 支持多种流行的嵌入式处理器架构，包括ARC、ARM、MIPS、RISC-V、x86等，这使得它能够应用于各种类型的嵌入式系统。

    4.BSD-like许可证: Picolibc采用BSD-like许可证，这意味着它可以自由地用于商业和非商业项目，这对于开发者来说非常友好。

    5.持续的维护和更新: Picolibc项目一直保持活跃，不断地进行维护和更新，修复bug，并添加新的功能，确保其稳定性和兼容性。

架构支持

    Picolibc已经完成了对许多架构的集成测试支持，这用于验证所有补丁集成。已支持的架构包括：ARC (32- and 64- bit)、ARM (32- and 64- bit)、i386、Motorola 68000 (m68k)、MIPS、MSP430、Nios II、Power9、RISC-V (32- and 64- bit)、SparcV8 (32 bit)、x86_64。此外，还有一些架构具有构建基础设施和持续构建验证，但没有集成的测试可用，包括：Microblaze、PowerPC、Sparc64、Xtensa (ESP8266, ESP32)。

与Newlib的关系

    Picolibc主要由Newlib的部分代码构建而成，并保留了该项目的目录结构。虽然构建系统和每个线程的数据存储方面进行了很多更改，但大部分源代码保持不变。为了保持picolibc和newlib代码同步，将定期合并newlib更改。

文档说明

    Picolibc提供了丰富的文档，涵盖了构建、使用、链接等各个方面。文档分为入门文档和详细文档两部分，入门文档介绍了如何编译和使用Picolibc，详细文档则深入探讨了Picolibc的内部机制和使用方法。


使用Meson构建系统在 GCC 的 Linux 主机上为各种硬件平台编译源代码树。除了默认参数外，该库还允许构建多个属性，通过使用属性行“-D=”来启用和禁用各种功能。即使 Picolibc 在来自 avrlibc 的小型 stdio 库上运行，用户也可以选择使用属性 (newlib-tinystdio=false) 来启用旧的 newlibc stdio 库。默认情况下它会是 newlib-tinystdio=true，这意味着它将使用基于 avrlibc 的小型 stdio 进行编译。

在开始构建 picolibc 之前要确保meson构建系统是构建环境的一部分，具体请参阅meson的设置说明；作为meson标准，交叉编译构建的所有构建配置都是单独配置文件的一部分，也是源代码树的一部分。源码树本身提供了用于各种架构的配置脚本，包括riscv、arm、x86等。

Zephyr参考页面上有其使用参考。

Picolibc是一款功能强大、轻量高效的C标准库，非常适合资源受限的嵌入式系统开发。其丰富的架构支持、BSD-like许可证以及活跃的社区都使其成为嵌入式开发者的理想选择。如果您正在寻找一款轻量级、高效且易于使用的C标准库，那么Picolibc绝对值得一试。