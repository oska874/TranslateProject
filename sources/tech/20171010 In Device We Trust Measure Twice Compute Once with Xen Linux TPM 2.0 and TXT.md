ezio is translating


在我们信任的设备：使用 Linux， TPM 2.0 ， 以及 TXT，测量两次，计算一次
============================================================


![software integration](https://www.linux.com/sites/lcom/files/styles/rendered_file/public/puzzle.jpg?itok=_B0M93-p "software integration")
Xen 虚拟化是的新颖的程序可以简单的集成到测试过的，能够共同操作的软件组件，运行在通用硬件上。[Creative Commons Zero][1]Pixabay

目标设备是个小平板或者大手机？是个手机还是个广播传感器？是服务器还是虚拟桌面集群？是 x86 模拟的 ARM，还是反过来？是 Linux 鼓舞了 Windows，或者还是反其道？是安全或者质量升级？_我现在的设备和以前的一模一样？当我们观察我们进化中的设备以及他们的远程服务，我们可以提出什么样的问题和测量手段？_

### 通用 vs 专用生态系统

通用计算机现在生存在一堆专用设备和信息家电中。但是 _在_ 设备中的软件和硬件组件的复杂性不断提高，边界不断模糊。通过 x86 和 arm 平台上的硬件虚拟化，多个操作系统的生态可以共存在一个设备上。模块化和可扩展的多厂家架构可以在垂直集成产品的盈利能力上和但一个厂家竞争码？

操作系统随着盈利市场的应用一起进化。PC 桌面是被商业生产力和媒体创造力驱动的。网络浏览器抽象了 OS 的不同，随着软件盈利模式转向电子商务，服务，和广告。移动设备针对内容与通信增加了传感器，无线电和硬件解码器。Apple，现在是最盈利的电脑公司，将软件和服务与传感器和硬件进行垂直集成。其它公司货币化了数据，提高了存储器的价值和存储优化。

有些市场需要信息安全和功能安全认证：汽车，飞行器，航海，交叉领域，工业控制，经济，能源，医药，以及嵌入式设备。作为软件“吞噬世界”，我们该如如何在没有企业和消费市场的规模经济的情况下使得垂直市场[现代化][5]？一个答案是来自基于硬件虚拟化的设备架构，Xen，[解集][6]，OpenEmbedded Linux 和测量发射。[OpenXT][7] 派生物使用可扩展的，开源基础，来增强在通用硬件上的专有应用的策略，重用可互相操作的组件。

[OpenEmbedded][8] Linux 支持一系列的 X86 和 arm 设备，而 Xen 隔离了操作系统和 [unikernels][9]。从不同生态得到的应用和驱动可以同时运行，扩展了技术和授权选项。特殊用途的软件可安全的和通过软件组装在一起，通过虚拟机隔离，由客户和 OEM 的策略定义的由硬件辅助的信任根来锚定。这种架构允许专用软件生厂商分享平台和硬件支持花费，而支持新兴的的传统的软件生态系统的定价是不同的。

### 站在硬件，固件和软件开发者的肩膀上

 ![0eMLJYIX3yDSWwbPA-1nhpPwza2JM2m_zJ7Idh41](https://lh3.googleusercontent.com/0eMLJYIX3yDSWwbPA-1nhpPwza2JM2m_zJ7Idh417_NW8eESi2rbXHjsUnMURaXRxV8vekNB6EVV4dBheddUZDgjmk3VkKUOSDzY0aFnPf6-LFquwNzoUVZAKeTH5iBSDzWjCHQFx8dh7zdgyQ) 

 _系统架构, from NIST SP800-193 (草稿), Platform Firmware Resiliency_ 

在用户面对软件应用的时刻，开始执行一个断电的硬件设备，一系列的固件和软件准备好了运行在平台上。专用设施的信息安全和功能安全的检查依赖于平台固件和计算设备的“信任根”的开发者。

如果我们针对计算设备考虑宇宙哲学 “[海龟垂钓][2]” 问题，那么根本的新人就是最底层的硬件，固件和软件的组合，是最初对关键安全功能和持久状态的信任。用在根信任的硬件组件包括 TCG 的安全平台模块（[TPM][10]），ARM 的基于 [TrustZone][11] 的可信执行环境（[TEE][12]），Apple 的 [安全领土][13] 协处理器（[SEP][14]），以及 Intel 在 x86 处理器的管理引擎（[ME][15]）。在 2015年 [TPM 2.0][16] 被接受作为 ISO 标准，在 2017 年被广泛应用与各种设备。

TPMs enable key authentication, integrity measurement and remote attestation. TPM key generation uses a hardware random number generator, with private keys that never leave the chip. TPM integrity measurement functions ensure that sensitive data like private keys are only used by trusted code. When software is provisioned, its cryptographic hash is used to extend a chain of hashes in TPM Platform Configuration Registers (PCRs). When the device boots, sensitive data is only unsealed if measurements of running software can recreate the PCR hash chain that was present at the time of sealing. PCRs record the aggregate result of extending hashes, while the TPM Event Log records the hash chain.  

Measurements are calculated by hardware, firmware and software external to the TPM. There are Static (SRTM) and Dynamic (DRTM) Roots of Trust for Measurement. SRTM begins at device boot when the BIOS boot block measures BIOS before execution. The BIOS then execute, extending configuration and option ROM measurements into static PCRs 0-7\. TPM-aware boot loaders like TrustedGrub can extend a measurement chain from BIOS up to the [Linux kernel][17]. These software identity measurements enable relying parties to make trusted decisions within [specific workflows][18].

DRTM enables "late launch" of a trusted environment from an untrusted one at an arbitrary time, using Intel's Trusted Execution Technology ([TXT][19]) or AMD's Secure Virtual Machine ([SVM][20]). With Intel TXT, the CPU instruction SENTER resets CPUs to a known state, clears dynamic PCRs 17-22 and validates the Intel SINIT ACM binary to measure Intel’s tboot MLE, which can then measure Xen, Linux or other components. In 2008, Carnegie Mellon's [Flicker][21] used late launch to minimize the Trusted Computing Base (TCB) for isolated execution of sensitive code on AMD devices, during the interval between suspend/resume of untrusted Linux.  

If DRTM enables launch of a trusted Xen or Linux environment without reboot, is SRTM still needed? Yes, because [attacks][22] are possible via privileged System Management Mode (SMM) firmware, UEFI Boot/Runtime Services, Intel ME firmware, or Intel Active Management Technology (AMT) firmware. Measurements for these components can be extended into static PCRs, to ensure they have not been modified since provisioning. In 2015, Intel released documentation and reference code for an SMI Transfer Monitor ([STM][23]), which can isolate SMM firmware on VT-capable systems. As of September 2017, an OEM-supported STM is not yet available to improve the security of Intel TXT.

Can customers secure devices while retaining control over firmware?  UEFI Secure Boot requires a signed boot loader, but customers can define root certificates. Intel [Boot Guard][24] provides OEMs with validation of the BIOS boot block.  _Verified Boot_  requires a signed boot block and the OEM's root certificate is fused into the CPU to restrict firmware.  _Measured Boot_  extends the boot block hash into a TPM PCR, where it can be used for measured launch of customer-selected firmware. Sadly, no OEM has yet shipped devices which implement ONLY the Measured Boot option of Boot Guard.

### Measured Launch with Xen on General Purpose Devices

[OpenXT 7.0][25] has entered release candidate status, with support for Kaby Lake devices, TPM 2.0, OE [meta-measured][3], and [forward seal][26] (upgrade with pre-computed PCRs).  

[OpenXT 6.0][27] on a Dell T20 Haswell Xeon microserver, after adding a SATA controller, low-power AMD GPU and dual-port Broadcom NIC, can be configured with measured launch of Windows 7 GPU p/t, FreeNAS 9.3 SATA p/t, pfSense 2.3.4, Debian Wheezy, OpenBSD 6.0, and three NICs, one per passthrough driver VM.

Does this demonstrate a storage device, build server, firewall, middlebox, desktop, or all of the above? With architectures similar to [Qubes][28] and [OpenXT][29] derivatives, we can combine specialized applications with best-of-breed software from multiple ecosystems. A strength of one operating system can address the weakness of another.

### Measurement and Complexity in Software Supply Chains

While ransomware trumpets cryptocurrency demands to shocked users, low-level malware often emulates Sherlock Holmes: the user sees no one. Malware authors modify code behavior in response to “our method of questioning”, simulating heisenbugs. As system architects pile abstractions, [self-similarity][30] appears as hardware, microcode, emulator, firmware, microkernel, hypervisor, operating system, virtual machine, namespace, nesting, runtime, and compiler expand onto neighboring territory. There are no silver bullets to neutralize these threats, but cryptographic measurement of source code and stateless components enables whitelisting and policy enforcement in multi-vendor supply chains.

Even for special-purpose devices, the user experience bar is defined by mass-market computing. Meanwhile, Moore’s Law is ending, ARM remains fragmented, x86 PC volume is flat, new co-processors and APIs multiply, threats mutate and demand for security expertise outpaces the talent pool. In vertical markets which need usable, securable and affordable special-purpose devices, Xen virtualization enables innovative applications to be economically integrated with measured, interoperable software components on general-purpose hardware. OpenXT is an open-source showcase for this scalable ecosystem. Further work is planned on reference architectures for measured disaggregation with Xen and OpenEmbedded Linux.

--------------------------------------------------------------------------------

via: https://www.linux.com/blog//event/elce/2017/10/device-we-trust-measure-twice-compute-once-xen-linux-tpm-20-and-txt

作者：[RICH PERSAUD][a]
译者：[译者ID](https://github.com/译者ID)
校对：[校对者ID](https://github.com/校对者ID)

本文由 [LCTT](https://github.com/LCTT/TranslateProject) 原创编译，[Linux中国](https://linux.cn/) 荣誉推出

[a]:https://www.linux.com/users/rpersaud
[1]:https://www.linux.com/licenses/category/creative-commons-zero
[2]:https://en.wikipedia.org/wiki/Turtles_all_the_way_down
[3]:https://layers.openembedded.org/layerindex/branch/master/layer/meta-measured/
[4]:https://www.linux.com/files/images/puzzlejpg
[5]:http://mailchi.mp/iotpodcast/stacey-on-iot-if-ge-cant-master-industrial-iot-who-can
[6]:https://www.xenproject.org/directory/directory/research/45-breaking-up-is-hard-to-do-security-and-functionality-in-a-commodity-hypervisor.html
[7]:http://openxt.org/
[8]:https://wiki.xenproject.org/wiki/Category:OpenEmbedded
[9]:https://wiki.xenproject.org/wiki/Unikernels
[10]:http://www.cs.unh.edu/~it666/reading_list/Hardware/tpm_fundamentals.pdf
[11]:https://developer.arm.com/technologies/trustzone
[12]:https://www.arm.com/products/processors/technologies/trustzone/tee-smc.php
[13]:http://mista.nu/research/sep-paper.pdf
[14]:https://www.blackhat.com/docs/us-16/materials/us-16-Mandt-Demystifying-The-Secure-Enclave-Processor.pdf
[15]:https://link.springer.com/book/10.1007/978-1-4302-6572-6
[16]:https://fosdem.org/2017/schedule/event/tpm2/attachments/slides/1517/export/events/attachments/tpm2/slides/1517/FOSDEM___TPM2_0_practical_usage.pdf
[17]:https://mjg59.dreamwidth.org/48897.html
[18]:https://docs.microsoft.com/en-us/windows/threat-protection/secure-the-windows-10-boot-process
[19]:https://www.intel.com/content/www/us/en/software-developers/intel-txt-software-development-guide.html
[20]:http://support.amd.com/TechDocs/24593.pdf
[21]:https://www.cs.unc.edu/~reiter/papers/2008/EuroSys.pdf
[22]:http://invisiblethingslab.com/resources/bh09dc/Attacking%20Intel%20TXT%20-%20paper.pdf
[23]:https://firmware.intel.com/content/smi-transfer-monitor-stm
[24]:https://software.intel.com/en-us/blogs/2015/02/20/tricky-world-securing-firmware
[25]:https://openxt.atlassian.net/wiki/spaces/OD/pages/96567309/OpenXT+7.x+Builds
[26]:https://openxt.atlassian.net/wiki/spaces/DC/pages/81035265/Measured+Launch
[27]:https://openxt.atlassian.net/wiki/spaces/OD/pages/96436271/OpenXT+6.x+Builds
[28]:http://qubes-os.org/
[29]:http://openxt.org/
[30]:https://en.m.wikipedia.org/wiki/Self-similarity
