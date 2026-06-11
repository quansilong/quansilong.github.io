---
title:  QuantumEspresso使用
date: 2026-06-11 16:30:00 +0800
categories: [Research]
tags: [QE, DFT, AIMD]
---

## QuantumEspresso使用

input.in
参考Input File Description
! !开头是注释
! 控制参数模块
! &开始
! /结束
&CONTROL
calculation="scf", !scf自洽计算
restart_mode="from_scratch", !从之前的计算结果中提取
prefix="Pt", !前缀
pseudo_dir=".",
outdir="./out",
!! ...更多参数
/
&SYSTEM
!计算体系
ibrav=1, !晶格的形状，参见https://www.quantum-espresso.org/Doc/INPUT_PW.html!idm199
celldm(1)=4.981, !晶格参数a,b,c,cos(ab),cos(ac),cos(bc)
!a,b,c是原子单位制 1 Bohr = 1 a0= 0.52917720859˚A
!1˚A=1/0.52917720859=1.8897
!根据ibrav参数，决定要设置的celldm(i),i的取值范围
#celldm()也可以用A,B,C,(单位是Angstrom)，cosAB, cosAC, cosBC代替
nat=1, ! num of atoms
ntyp=1, ! type of species ,同位素，赝势不同，等为不同species
ecutwfc=25, !原子单位制Ry
!1 Ry = 1/2 Ha = 13.60569193 eV
!与VASP相同，可以通过看赝势选择
!ecutrho=, !与赝势,ecutwfc能有关
!越大收敛的越平滑，震荡少
!default 4 times ecutwfc
/
&ELECTRONS
!电子步参数
mixing_beta=0.7, !old 30% new 70%
conv_thr=1d-6, !收敛标准
/
![ &IONS
! ...
! / ]
![ &CELL
! ...
! / ]
ATOMIC_SPECIES
Pt 195.084 Pt.pbe-n-kjpaw_psl.1.0.0.UPF
! X Mass_X PseudoPot_X
! Y Mass_Y PseudoPot_Y
! Z Mass_Z PseudoPot_Z
ATOMIC_POSITIONS alat
Pt 0.0 0.0 0.0
!{ alat 分子坐标| bohr | crystal | angstrom | crystal_sg }
! X 0.0 0.0 0.0 {if_pos(1) if_pos(2) if_pos(3)}
! Y 0.5 0.0 0.0
! Z O.0 0.2 0.2
!控制行不能这样注释
!ATOMIC_POSITIONS alat !{ alat 分子坐标| bohr | crystal | angstrom | crystal_sg }
K_POINTS automatic
10 10 10 1 1 1
!{ tpiba | automatic 自动 | crystal | gamma 只有k=0的点| tpiba_b | crystal_b | tpiba_c |
crystal_c }
!if (gamma)
! nothing to read
!if (automatic)
! nk1, nk2, nk3, k1, k2, k3
#nk1,nk2,nk3 k点数量， k1,k2,k3平移项，只能0，1
#nk为奇数时，取样过gamma点
!if (not automatic)
! nks
! xk_x, xk_y, xk_z, wk
赝势
下载pseudopotentials
里面有建议的截断能
Suggested minimum cutoff for wavefunctions: 39. Ry
Suggested minimum cutoff for charge density: 401. Ry
可以帮助设置
ecutwfc=40, !要大于赝势中的Suggested minimum cutoff for wavefunctions: 39. Ry
!原子单位制Ry
!1 Ry = 1/2 Ha = 13.60569193 eV
!
!ecutrho=, ！!要大于赝势中的Suggested minimum cutoff for charge density: 401. Ry
!与赝势,ecutwfc能有关
!越大收敛的越平滑，震荡少
!default 4 times ecutwfc
