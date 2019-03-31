> README_old.md 的中文版本，有删改
# mrQ 手册（mrQ 2）
---

mrQ 是用于计算 SPGR（FLASH）系列扫描的 MR 参数（如 T1 和 PD）的软件包，通过 T1 和 PD 图像，mrQ 可以评估 macromolecular tissue volume （**MTV**）、volume of the interacting water protons（**VIP**） 以及 water-surface interaction rate （**SIR**）的值。

有问题可以联系：

>Aviv Mezer: aviv.mezer(AT)elsc.huji.ac.il
>
>Shai Berman: shai.berman(AT)mail.huji.ac.il  
>
>Jonathan Bain: jonathan.bain(AT)mail.huji.ac.il

在这个[论坛](https://groups.google.com/forum/#!forum/mrq-forum)里可以与大家互动讨论问题，获取更新等。

版本 2 （V.2）于 2015 年秋季释出，由 Mezer 和 VISTA 实验室合作完成。

相对于版本 1（V.1），V.2 有以下特点：
- 代码模块化，大量的注释增加可读性；
- **仅**支持 NIfTI 格式的文件，不再支持 DICOM 格式；建议使用 SciTran 用于 DICOM 到 NIfTI 格式的转换（因为该工具提供了必要的头信息），如果用其他转换工具，则需要手动添加那些头信息；
- 现在的 mrQ_run 函数整合了之前版本的 mrQ_Create、mrQ_Set、mrQ_run 三个函数的功能；
- 在 T1-M0 fit 中默认使用加权线性最小二乘法，而代替了之前版本的非线性最小二乘法，节约运行时间、提高准确度；
- parfor-loops 用于并行运算；

获取版本 2: https://github.com/mezera/mrQ/releases/tag/v.2

#软件依赖项

**必需项**

- [Matlab](http://www.mathworks.com/products/matlab/)，须包含以下工具包
  1. Image Processing Toolbox
  2. Optimization Toolbox
  3. Parallel Computing Toolbox
  4. Signal Processing Toolbox
  5. Simulink
  6. Statistics and Machine Learning Toolbox
  7. Symbolic Math Toolbox
  8. [SPM8](http://www.fil.ion.ucl.ac.uk/spm/software/spm8/)
  9. [Vistasoft](https://github.com/vistalab/vistasoft)
  10. [KNKUtils](https://github.com/kendrickkay/knkutils)
  11. [mrQ](https://github.com/mezera/mrQ)

- [ANTs](http://stnava.github.io/ANTs/)
- [FSL](http://fsl.fmrib.ox.ac.uk/fsl/fslwiki/)


**可选项**

- [FreeSurfer](http://surfer.nmr.mgh.harvard.edu/)
- [Parallel computing environment(如，SunGrid 引擎)]
- DICOM-NIfTI 转换工具
  - 建议使用 [SciTran](http://scitran.github.io/)
  - 也可以使用其他 NIfTI 文件，此时需要手动输入头信息，请见；

### MRI 扫描设置 （暂且原文不动）
##### Spoiled gradient echo scans (SPGR, FLASH) #####

1. 2-4 SPGR (not fast SPGR) scans with multiple flip angles recommended (e.g., 4, 10, 20 and 30 degrees).  
2. All scans should have a single TR.
3. Scans should have a minimal TE, about 2 msec. (Longer TE generates T2* weighting)
4. *Optional*: Save the multi-coil information. 
   - *GE*: Change the scanner default by editing the saveinter cv: saveinter=1.
   - *Siemens*: To save the individual coil information:  System &#10142; Miscellaneous &#10142; Save Uncombined.
   - *Philips*: If you know how to implement these settings, please email us or post in the forum. 
5. Scan with the same prescan parameters for all SPGR scans. 
   - *GE*: Scan the highest SNR image first (flip angle = 10 degrees). For the next scan, choose manual pre-scan and perform the scan without changing the pre-scan parameters.
   - *Siemens*: For the other flip angles: Right-click the sequence in the protocol &#10142; Properties and then select a Tab called Execution. There is an option called 'scan without further preparation' and you need to check that box.  That will cause that sequence to copy the prescan parameters from the previous one.
   - *Philips*: If you know how to implement these settings, please email us or post in the forum. 

##### EPI spin-echo inversion recovery scans (B1 mapping) #####

Low-resolution T1 maps are used to correct for the B1 bias. We will acquire data to fit unbiased T1 maps and correct the bias in the SPGR scans. The T1 fit is based on the <a href=#matlab-code>aforementioned</a> article by Barral et al. (2010).

1. Scan four SEIR-EPI readout scans with four different inversion times (50, 400, 1200 and 2400 msec).
2. Each scan needs to be acquired with slab inversion. 
   - *GE*: Change the scanner default by editing the a_gzrf0 cv: a_gzrf0=0
   - *Siemens*: For slab selective: Routine &#10142; Concatenations and set that parameter to the number of slices.  That way the new slice will not be acquired before the old one is finished.
   - *Philips*: If you know how to implement these settings, please email us or post in the forum. 
3. Use fat suppression. Fat suppression is recommended to be spatial-spectral to avoid any slice-selective imperfections.
   - *GE*: This is the default when slices are less than 4 mm thick.
   - *Siemens*: There is a similar setting called "water excitation", but check with Siemens for technical specifics.
   - *Philips*: If you know how to implement these settings, please email us or post in the forum. 

Alternatively, you can provide your own B1 map (NIfTI) if it is in SPGR space.

mrQ 官方释出了两个版本，分别是 v.1、v.2，如无特别说明，均围绕 v.2 版本进行说明。
下面简述 v.1、v.2 这两个版本：
v.1 
  由 mrQ_Create、mrQ_Set 和 mrQ_run 分别负责 mrQ 结构体的构建、参数设置与运行。
v.2
  v.1 的上述三个函数整合成一个 mrQ_run 函数，同时保留了 mrQ_Create、mrQ_Set 两个函数；
  新的 mrQ_run 函数具备了 mrQ_Create、mrQ_Set 函数的功能，通过设置参数即可实现，mrQ_run 的第六个参数是一个 cell 结构的数据
  后面出了 v.2.1 版本，对 v.2 的代码进行了优化。
  
  
  根据 MR 图像（NIfTI 格式）是否带有一些头信息，可以将 mrQ_run 的工作模式分成两种。带头信息的 NIfTI 文件是由 DICOM 文件由 SciTran 提供的脚本转换而成。不带头信息的由普通的工具转换而成。

#### SciTran 转换的 NIfTI 文件 ####



#### 其他工具转换的 NIfTI 文件 ####
