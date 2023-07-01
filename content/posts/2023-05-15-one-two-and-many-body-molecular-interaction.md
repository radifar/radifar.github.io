---
title: One, Two, and Many Body Molecular Interaction
description: ""
date: 2023-05-15T13:34:14.135Z
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
slug: one-two-many-body-molecular-interaction
---

To be honest with you, after I wrote about how I categorise molecular interaction I got this itch to sratch the surface and build the simplest proof of concept for this DSL. But before that, lets finish this molecular interaction section so that we can completely move on. To complete this section I will talk about the number of body that should be included in the interaction.

Quite often, we fall under the assumption that interaction happen between two bodies. For example, we often curious about the interaction between a drug and a receptor, an enzyme and its inhibitor, protein and DNA, antibody and antigen, and many more. What some (or many) of us failed to notice is how important intramolecular (one body) interaction and many ways interaction. Fassio et.al. (2022) demonstrate how EIFP outperform two other IFP representation then pointed out that it is important to note that EIFP incorporate intraresidue interaction and although 
