---
title: Molecular Interaction Revisited
description: ""
date: 2023-05-07T09:57:02.907Z
preview: ""
draft: true
tags:
  - python
  - lark
  - DSL
  - interaction-fingerprinting
categories:
  - computational-chemistry
series:
  - Deemian-DSL
slug: molecular-interaction-revisited
---

In the previous post, I explained a bit about DSL and why we need DSL for molecular interaction analysis. In this post, I will elaborate on the kind of interactions that I would like to cover and implement in our DSL. Traditionally, molecular interaction refers to the non-covalent interactions between two atoms or groups of atoms. But to cover many edge use cases, I would like to explain molecular interaction exhaustively using loose definitions.

To do so, I will classify the interaction into five categories (or descriptor scheme). Start with the non-covalent interactions, then contact- or proximity-based approach, moiety/group interaction, pharmacophore or hotspot-based interaction, and anti-interaction.

For each interaction scheme, I will start with the definition, how it begin to exist and how it evolved from time to time, and the significance of this scheme, especially in drug discovery research. And when possible, I would like to discuss how each interaction can be presented and scored using the scoring function.

## Non-covalent Interactions

Right after the humanity figure out the structure of an atom and how atom bonded to each other via covalent or ionic bond, we shift a lot of our attention to non-covalent interactions. We believe that the non-covalent interaction is the key to many substance properties and behavior. In molecular biology and medicinal chemistry we know that non-covalent interaction govern subcellular, cellular, or even physiological activities, these include molecular packing, signal transduction of hormone and neurotransmitter, cell differentiation, antigen recognition by antibody, directed cellular movement, etc.

No matter how complex the interactions are, there are pattern for every activity which can be discovered through analysis. The first attempt to analyse the pattern of these interaction was begin with the invention of structural interaction fingerprint (SIFt) by Deng et al. (2004). SIFt classify non-covalent interactions into seven interactions: contact, mainchain, sidechain, polar, nonpolar, H-bond acceptor, and H-bond donor.

Later Marcou and Rognan (2007) developed a more fine-grained IFP based on SIFt, for example nonspecific interaction like contact, mainchain, and sidechain are removed. While nonpolar can be divided into hydrophobic and aromatic interaction, the latter can be further divided into face-to-face and edge-to-face, and thus the nonpolar interaction divided into three interactions. Likewise the polar interaction can be divided into two ionic/electrostatic interaction (protein as cation or as anion), hydrogen bond is actually also polar but it deserves its own definition due to its highly specific manner. Hydrogen bond itself can be divided into strong H-bond and weak H-bond, and each of them can be divided into two, protein as H-bond donor or acceptor, which give four different H-bond. Last, there are two miscellaneous interactions, pi-cation and metal complexation. Overall there are 11 types of interaction in the IFP method described by Marcou and Rognan, and it became the basis of IFP method used by various IFP tools like PyPLIF, Arpeggio, PLIP, ProLIF, FinggeRNAt etc.

The term fingerprint in IFP is actually came from the specific Boolean pattern that derive from the interaction between two molecules. As this method was born from the need to discover new drug candidate from chemical library using protein structure, protein residue became the de facto unit of a fingerprint. Thus, IFP is actually an aggregation of a set of interactions in a residue encoded in Boolean value. The more interactions and residues involved, the more specific and/or noisy the pattern is.

The resulting fingerprint can be used in many ways, but the most common one is to score the pattern based on known reference.

## Contact- or Proximity-based approach

This is especially important for coarse-grained interaction like in MARTINI forcefield.

Various descriptor scheme. Element, Sybil, CREDO.

## Moiety or group interactions

Substructure to derive semantic. (Moltrans)

Interaction with dynamic secondary structure. (See NCIPlot)

## Pharmacophore or hotspot-based interactions

## Anti-interaction

Often when it comes to biological activity such as enzyme inhibition, receptor agonism/antagonism, antigen-antibody binding, and drug selectivity, we pay too much attention to attractive interaction and pretend like the repulsive interaction doesn't exist.

A quick search of the literature using the keywords `drug, scoring, repulsion, and penalty` shows that penalty or repulsion are mostly incorporated on force-field based scoring function. And when it comes to interaction fingerprinting there is almost no mention of repulsion, penalty, or even anti-interaction terms. Even in 2009, when interaction fingerprinting was still new, it was clear that this method can not differentiate attractive from repulsive interaction on hydrophobic interaction ([Vijayan et al., 2009](https://doi.org/10.1021/ci900309s)). At the time of this writing, I can only find two interaction fingerprinting method or tool that employs the repulsive term, PADIF ([Jasper et al., 2018](https://doi.org/10.1186/s13321-018-0264-0)) and LUNA ([Fassio et al., 2022](https://doi.org/10.1021/acs.jcim.2c00695)). And perhaps it is worth mentioning that Arpeggio ([Jubb et al., 2017](https://doi.org/10.1016/j.jmb.2016.12.004)) is able to identify steric clashes, but this tool is meant for interaction identification and visualisation rather than scoring and virtual screening.

In my opinion, repulsive interaction could be useful in some IFP-based virtual screening campaigns yet often overlooked and stay in the blind spot for far too long. It can be seen by how we score similarity between two interaction fingerprints through additive fashion rather than combining additive and subtractive terms. This is odd as the repulsive interaction is included in the docking scoring function but not in the IFP scoring function when IFP is most of the time used as a post-docking analysis tool. Ideally, we should combine them by identifying interaction and anti-interaction and then adding the penalty from the weighted anti-interaction. This way we can get a more comprehensive and objective view of the molecular interactions.

Now I would like to clarify the meaning of anti-interaction. As there is agonism versus antagonism, pattern versus antipattern, I choose the anti-interaction rather than repulsive interaction. In general, anti-interaction means unfavourable interactions, which can be electrostatically, enthalpically, or entropically unfavourable. And this is why I prefer not to choose repulsive interaction. For example, in the case of the water molecule inside a hydrophobic cage, the water itself is not being repulsed. Instead, the water became enthalpically unfavourable as it was unable to form a few hydrogen bonds inside the cage.

I also have to clarify that anti-interaction not only cover non-covalent interaction. And there is a reason why I explain anti-interaction in the last part, because anti-interaction can happen in all previous group except the contact or proximity-based group.

First lets explore non-covalent anti-interaction. This subcategory include steric clash, repulsion between same charged atom, and different polarity interaction.

The second subcategory is Moiety or group anti-interaction.

The last one is pharmacophore or hotspot-based anti-interaction.
