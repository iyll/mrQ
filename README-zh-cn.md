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
- [Matlab](http://www.mathworks.com/products/matlab/)
  - 需配置的工具包
- [ANTs](http://stnava.github.io/ANTs/)
- [FSL](http://fsl.fmrib.ox.ac.uk/fsl/fslwiki/)
- [SPM8](http://www.fil.ion.ucl.ac.uk/spm/software/spm8/)

**可选项**
- [FreeSurfer](http://surfer.nmr.mgh.harvard.edu/)
- [Parallel computing environment(如，SunGrid 引擎)]
- DICOM-NIfTI 转换工具
  - 建议使用 [SciTran](http://scitran.github.io/)
  - 也可以使用其他 NIfTI 文件，此时需要手动输入头信息，请见；

**Matlab 代码**
- mrQ
- [Vistasoft](https://github.com/vistalab/vistasoft)
- [KNKUtils](https://github.com/kendrickkay/knkutils)
