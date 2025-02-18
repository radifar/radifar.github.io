---
title: Protein protonation tools and a simple case study how to use them for MD structure preparation with GROMACS
description: ""
date: 2025-02-18T03:50:51.615Z
preview: ""
draft: false
tags:
    - Molecular Modeling
    - Protonation
    - Molecular Simulation
    - Protein
    - pK
categories: []
---

In the previous post I wrote about why protonation state determination is important and the various method that can be used to determine protonation state. In this post I would like to do a simple case study on how to use empirical-based pK or protonation state prediction using PROPKA and how to use this tool to help preparing the input structure and topology for GROMACS.

As a disclaimer, this post does not contain the whole step on how to use pK prediction tool result to guide the preparation of protein structure prior to MD simulation, rather this is only a guide that could be used as pointer and give insight on how straightforward it is to integrate pK prediction tool like PROPKA in preparing "correctly" protonated protein structure for MD simulation.

## Why PROPKA?

Nowadays PROPKA is widely used for preparing initial protein structure for MD simulation. Many people not realize it but CHARMM GUI is using PROPKA for protonation state determination. Therefore we could use it to compare the result with MD simulation that run on CHARMM. Due to its nature as empirical pK prediction tool, it is fast and have pretty good accuracy when compared with experimental data as shown in a study by Olsson et al (2010).

PROPKA is also free to use and well documented. The output from PROPKA is human readable and at the same time can be easily parsed with simple Python script. This is very important as explained later in protein protonation automation section below.

## The input and the end goal

We will start from the struture for coagulation factor Xa (PDB ID: 1fjs). Then from here we will retrieve the predicted pK values from PROPKA and use it to prepare the structure in "correctly" protonated form and its corresponding topology file using gmx2pdb tool. The topology that is used will be derived from CHARMM27 forcefield rtp file that is integrated within GROMACS.

## Things to consider before integrating PROPKA and GROMACS

PROPKA and CHARMM have their own subset of acceptable titratable residues. For example as can be seen in the following PROPKA output excerpt, PROPKA only calculate the pK of ASP, GLU, HIS, TYR, LYS, and ARG. In this calculation CYS is never considered as titratable and therefore given the pK value of 99.99. On the other hand CHARMM, as shown by its `aminoacids.rtp` file in GROMACS data directory, only support the neutral form of ASP, GLU, and LYS but not ARG. And it supports protonated HIS. And only have the topology for neutral CYS and TYR but not the deprotonated form. Therefore we can only multiple protonation state for ASP, GLU, HIS, and LYS only, as these are the only residue that supported by both tool.

```
SUMMARY OF THIS PREDICTION
       Group      pKa  model-pKa   ligand atom-type
   ASP  24 A     3.10       3.80                      
   ASP  70 A     4.01       3.80                      
   ASP 100 A     3.49       3.80                      
   ASP 102 A     3.40       3.80                      
   ASP 126 A     4.00       3.80                      
   ASP 164 A     2.43       3.80                      
   ASP 185 A     3.30       3.80                      
   ASP 189 A     4.49       3.80                      
   ASP 194 A     3.91       3.80                      
   ASP 205 A     3.52       3.80                      
   ASP 239 A     2.04       3.80                      
   ASP  92 L     3.79       3.80                      
   ASP  95 L     2.73       3.80                      
   ASP  97 L     3.88       3.80                      
   ASP 119 L     3.88       3.80                      
   GLU  21 A     4.05       4.50                      
   GLU  26 A     4.29       4.50                      
   GLU  36 A     3.84       4.50                      
   GLU  37 A     4.89       4.50                      
   GLU  39 A     3.99       4.50                      
   GLU  49 A     4.80       4.50                      
   GLU  74 A     4.20       4.50                      
   GLU  76 A     4.29       4.50                      
   GLU  77 A     6.43       4.50                      
   GLU  80 A     1.62       4.50                      
   GLU  84 A     3.44       4.50                      
   GLU  86 A     4.49       4.50                      
   GLU  97 A     4.66       4.50                      
   GLU 124 A     5.53       4.50                      
   GLU 129 A     3.34       4.50                      
   GLU 146 A     2.83       4.50                      
   GLU 159 A     4.11       4.50                      
   GLU 188 A     3.38       4.50                      
   GLU 217 A     4.04       4.50                      
   GLU 102 L     4.23       4.50                      
   GLU 103 L     4.59       4.50                      
   GLU 138 L     5.07       4.50                      
   HIS  57 A     7.13       6.50                      
   HIS  83 A     6.00       6.50                      
   HIS  91 A     4.69       6.50                      
   HIS 145 A     6.11       6.50                      
   HIS 199 A     3.25       6.50                      
   HIS 101 L     5.94       6.50                      
   CYS  22 A    99.99       9.00                      
   CYS  27 A    99.99       9.00                      
   CYS  42 A    99.99       9.00                      
   CYS  58 A    99.99       9.00                      
   CYS 122 A    99.99       9.00                      
   CYS 168 A    99.99       9.00                      
   CYS 182 A    99.99       9.00                      
   CYS 191 A    99.99       9.00                      
   CYS 220 A    99.99       9.00                      
   CYS  89 L    99.99       9.00                      
   CYS  96 L    99.99       9.00                      
   CYS 100 L    99.99       9.00                      
   CYS 109 L    99.99       9.00                      
   CYS 111 L    99.99       9.00                      
   CYS 124 L    99.99       9.00                      
   CYS 132 L    99.99       9.00                      
   TYR  51 A    10.63      10.00                      
   TYR  60 A     9.00      10.00                      
   TYR  99 A    12.00      10.00                      
   TYR 162 A    12.12      10.00                      
   TYR 185 A    11.08      10.00                      
   TYR 207 A    11.43      10.00                      
   TYR 225 A    12.05      10.00                      
   TYR 228 A    13.92      10.00                      
   TYR 115 L    12.52      10.00                      
   TYR 130 L    10.70      10.00                      
   LYS  23 A    10.39      10.50                      
   LYS  62 A    10.60      10.50                      
   LYS  65 A    11.26      10.50                      
   LYS  90 A    10.65      10.50                      
   LYS  96 A    11.00      10.50                      
   LYS 109 A    10.69      10.50                      
   LYS 134 A    10.28      10.50                      
   LYS 147 A    10.52      10.50                      
   LYS 156 A     9.29      10.50                      
   LYS 169 A    10.11      10.50                      
   LYS 186 A    11.66      10.50                      
   LYS 204 A    10.55      10.50                      
   LYS 223 A    10.39      10.50                      
   LYS 224 A    10.43      10.50                      
   LYS 230 A     9.12      10.50                      
   LYS 236 A    10.85      10.50                      
   LYS 243 A    11.25      10.50                      
   LYS  87 L    10.30      10.50                      
   LYS 122 L    11.41      10.50                      
   LYS 134 L     9.60      10.50                      
   ARG  63 A    12.44      12.50                      
   ARG  67 A    11.86      12.50                      
   ARG  71 A    11.84      12.50                      
   ARG  93 A    12.44      12.50                      
   ARG 107 A    12.78      12.50                      
   ARG 115 A    12.01      12.50                      
   ARG 125 A    13.73      12.50                      
   ARG 143 A    11.94      12.50                      
   ARG 150 A    12.31      12.50                      
   ARG 154 A    12.41      12.50                      
   ARG 165 A    11.48      12.50                      
   ARG 202 A    12.52      12.50                      
   ARG 222 A    13.72      12.50                      
   ARG 240 A    12.18      12.50                      
   ARG 113 L    10.61      12.50                      
   N+   16 A     6.87       8.00                      
   N+   87 L     7.89       8.00
   ```

## The two possible approach to the end goal

To my knowledge there are two approach that can be used to create the correctly protonated structure. The first one is more straightforward, which is by taking the pK value of each titratable residue and feed it to the gmx2pdb using the `-inter` option, for example by following the official [GROMACS tutorial](https://tutorials.gromacs.org/docs/md-intro-tutorial.html) and adding the `-inter` option will ask the protonation state for each titratable residue like so:

```
> gmx pdb2gmx -f 1fjs_protein.pdb -o 1fjs_processed.gro -inter -water tip3p -ff charmm27

...

Processing chain 1 'A' (1852 atoms, 234 residues)
Which LYSINE type do you want for residue 8
0. Not protonated (charge 0) (LSN)
1. Protonated (charge +1) (LYS)

Type a number:1
Which LYSINE type do you want for residue 48
0. Not protonated (charge 0) (LSN)
1. Protonated (charge +1) (LYS)

Type a number:1
Which LYSINE type do you want for residue 51
0. Not protonated (charge 0) (LSN)
1. Protonated (charge +1) (LYS)

Type a number:

```

As we can see in the interactive mode above `gmx2pdb` will ask the protonation state for every residue which is quite tedious. However, it is possible to automate this interaction using Python or shell script. And as I mentioned in the disclaimer before, I would not write the proof of concept for this script. So this is merely serve as guidance for those whom willing to automate this task.

The second approach is by reinventing the wheel, what I mean by this is by rewriting `pdb2gmx` tool using Python script so that the script can automatically read and parse the pK value from PROPKA then use it to protonate the PDB accordingly using cheminformatics tool like Openbabel or RDKit. Then writing the corresponding topology file based on the structure and its protonation state. Though this step is more complicated, it is easier to test and understand what happens in the background. As it is more modular it can be used for different forcefield and even different MD tool.

## Conclusion

It is possible to automate the protonation using the pK calculation results from tools like PROPKA and use it to protonate the structure and prepare its corresponding topology file. The caveat is that there are discrepancies between the acceptable titratable residues for each tools. And for structure and topology preparation tool like pdb2gmx unfortunately the protonation states have to be selected through interactive mode, which add hurdle to the automation effort. And although this post is specifically used PROPKA and GROMACS it is possible to apply this methods to other tools as well.