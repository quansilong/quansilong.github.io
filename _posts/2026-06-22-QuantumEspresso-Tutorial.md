---
title:  Quantum Espresso Tutorial
date: 2026-06-22 16:30:00 +0800
categories: [Research]
tags: [QE, DFT, AIMD]
---

# Quantum Espresso 使用教程 ——by Silong Quan

## 1 登陆服务器 (如在本地计算可跳过此步)
- 下载安装[Xshell](https://cdn.netsarang.net/v8/Xshell-latest-p)和[Xftp](https://cdn.netsarang.net/v8/Xftp-latest-p)；
- 打开 Xshell，点击「新建」，点击「连接」填写主机地址、端口号等信息；再点击「用户身份验证」输入用户名与密码，完成后点击「确定」;
![主题](/assets/image/2026-06-22/1.png)

- 点击「打开」，选中刚刚新建的连接会话，点击「连接」登录服务器;
![主题](/assets/image/2026-06-22/2.png)

- 点击工具栏Xftp图标启动Xftp文件传输工具，窗口左侧为本地文件目录，右侧为服务器文件目录。
![主题](/assets/image/2026-06-22/3.png)

## 2 新建个人文件夹和工作目录
登陆服务器后，先新建自己的`个人文件夹`，以后所有的操作和计算在新建的个人文件夹中进行，如我以自己的姓名拼音新建个人文件夹「quansilong/」。新建个人文件夹后，进入`个人文件夹`（如「quansilong/」）. 
```console
msmcquan@inspur-NF5468M5:~$ mkdir quansilong
msmcquan@inspur-NF5468M5:~$ ll
total 4
drwxr-xr-x 3 msmcquan quansilong 4096 6月  15 10:39 quansilong/
msmcquan@inspur-NF5468M5:~$ cd quansilong/
msmcquan@inspur-NF5468M5:~/quansilong$ pwd
/home/msmcquan/quansilong
```

在`个人文件夹`（如「quansilong/」）中新建`工作目录`（如「LiFePO4-c-010-QE/」），并进入`工作目录「LiFePO4-c-010-QE/`。此教程将以计算 $LiFePO_4$的电子结构为例。
```console
msmcquan@inspur-NF5468M5:~/quansilong$ mkdir LiFePO4-c-010-QE
msmcquan@inspur-NF5468M5:~/quansilong$ cd LiFePO4-c-010-QE/
msmcquan@inspur-NF5468M5:~/quansilong/LiFePO4-c-010-QE$ pwd
/home/msmcquan/quansilong/LiFePO4-c-010-QE
```

## 3 准备PBS队列系统提交脚本
`PBS（Protable Batch System）`是功能最为齐全，历史最悠久，支持最广泛的本地集群调度器之一。 PBS的目前包括OpenPBS，PBS Pro和Torque三个主要分支。其中OpenPBS是最早的PBS系统，目前已经没有太多后续开发，PBS pro是PBS的商业版本，功能最为丰富。`Torque`是Clustering公司接过了OpenPBS，并给与后续支持的一个开源版本。

`Torque`作为一款开源免费的PBS排队管理系统，被许多使用服务器和集群的小组广泛使用。课题组服务器上已经部署好 `Torque PBS`任务排队管理系统。PBS队列系统提交脚本 `runqe-pw.pbs`我已写好，已上传到我的个人文件夹中。
```bash
msmcquan@inspur-NF5468M5:~/quansilong/LiFePO4-c-010-QE$ ll ..
total 12
drwxr-xr-x 2 msmcquan quansilong 4096 6月  24 09:39 LiFePO4-c-010-2x1x2-vac-QE/
drwxr-xr-x 2 msmcquan quansilong 4096 6月  24 09:40 LiFePO4-c-010-QE/
-rw-r--r-- 1 msmcquan quansilong  889 6月  24 15:20 runqe-pw.pbs
```

计算时，需要将PBS队列系统提交脚本 `runqe-pw.pbs`复制到自己计算的工作目录。如本教程中，需要将PBS队列系统提交脚本 `runqe-pw.pbs`复制到工作目录 `LiFePO4-c-010-QE/`中。
```bash
msmcquan@inspur-NF5468M5:~/quansilong/LiFePO4-c-010-QE$ cp ../runqe-pw.pbs .
msmcquan@inspur-NF5468M5:~/quansilong/LiFePO4-c-010-QE$ ll
total 4.0K
-rw-r--r-- 1 msmcquan quansilong 889 6月  24 16:10 runqe-pw.pbs
```

## 4 Quantum ESPRESSO计算
### 4.1 Quantum ESPRESSO介绍
Quantum ESPRESSO是一个开源的电子结构计算和材料模拟软件，它主要是基于密度泛函理论(DFT)，平面波和赝势理论，它的功能主要包括：
- 基态计算
  - 能量自洽，应力，Kohn—Sham轨道
  - 支持模守恒赝势(NCPP)和超软赝势(USPP)，缀加投影波(PAW)
  - 包含多种交换-关联泛函：从LDA到GGA(PW91, PBE, B88-P86, BLYP)再到meta-GGA, 再到精确交换(HF)和杂化泛函(PBE0, B3LYP, HSE)
  - 范德华修正：Grimme的D2,D3修正，XDM, 非局域范德华泛函
  - Hubbard U(DFT+U, DFT+U+V)
  - Berry相位极化
  - 非线性磁矩，自旋轨道耦合
- 结构优化，分子动力学和势能面
	- 准牛顿BFGS预处理的几何优化(GDIIS)
	- 阻尼动力学
	- Car-Parrinello分子动力学
	- 波恩-奥本海默分子动力学
	- 轻推弹性带(NEB)方法
- 量子输运
	- 弹道输运
	- 基于最大局域万尼尔函数(MLWF)的相干传输
	- 最大局域化万尼尔函数(MLWF)和输运性质
	- Kubo-Greenwood电导
- 电化学和特殊边界条件…
- 响应性质(微扰密度泛函理论)…
- 光谱性质...

值得一提的是，qe在电声相互作用、弛豫时间计算等方面做的非常好。截至目前，QE的最新版本7.5版本。

Quantum ESPRESSO是一套集成的开源软件套件，包含很多功能独立的程序模块。
```bash
quansilong@inspur-NF5468M5:~/soft/qe-7.5/bin$ pwd
/home/quansilong/soft/qe-7.5/bin
quansilong@inspur-NF5468M5:~/soft/qe-7.5/bin$ ls
alpha2f.x             disca.x         fermi_proj.x      kcwpp_interp.x  molecularnexafs.x     pioud.x     pprism.x        pwi2xsf.x             sumpdos.x         wfck2r.x
average.x             dist.x          fermi_velocity.x  kcwpp_sh.x      molecularpdos.x       plan_avg.x  pp_spctrlfn.x   pw.x                  turbo_davidson.x  wfdd.x
band_interpolation.x  dos.x           fqha.x            kcw.x           neb.x                 plotband.x  pp.x            q2qstar.x             turbo_eels.x      xspectra.x
bands_unfold.x        dvscf_q2r.x     fs.x              kpoints.x       nscf2supercond.x      plotproj.x  projwfc.x       q2r.x                 turbo_lanczos.x   ZG.x
bands.x               dynmat.x        gww_fit.x         lambda.x        open_grid.x           plotrho.x   pw2bgw.x        rism1d.x              turbo_magnon.x
bse_main.x            epa.x           gww.x             ld1.x           oscdft_et.x           pmw.x       pw2critic.x     scan_ibrav.x          turbo_spectrum.x
cell2ibrav.x          epsilon_Gaus.x  head.x            manycp.x        oscdft_pp.x           postahc.x   pw2gw.x         simple_bse.x          wannier2pw.x
cppp.x                epsilon.x       hp.x              manypw.x        path_interpolation.x  postw90.x   pw2wannier90.x  simple_ip.x           wannier90.x
cp.x                  epw.x           ibrav2cell.x      matdyn.x        phcg.x                ppacf.x     pw4gww.x        simple.x              wannier_ham.x
d3hess.x              ev.x            initial_state.x   merge_wann.x    ph.x                  pp_disca.x  pwcond.x        spectra_correction.x  wannier_plot.x
```

### 4.2 Quantum ESPRESSO输入文件

### 4.3 Quantum ESPRESSO计算流程



<font size="4" color="red"><b>这是红色加粗的大号字体</b></font>
