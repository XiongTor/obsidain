---
title: "Learning the language of phylogeny with MSA transformer"
authors: Ruyi Chen, Gabriel Foley, Mikael Bodén
year: 2024
---

<div style="font-size: 28px; color: #C97C7C; margin-top: 0px;">
  Learning the language of phylogeny with MSA transformer
</div>

[Zotero](zotero://select/items/@chenLearningLanguagePhylogeny2024)

<div style="
  border-radius: 8px;
  background-color: #2f2d2d;
  padding: 15px 20px;
  margin-top: 15px;
  color: #ddd6d6;
  line-height: 1.6;
  font-size: 16px;
">
  <div style="font-size: 22px;font-weight: bold; color: #bbb3b3; margin-bottom: 8px;">
    ❝ Abstract
  </div>
  Classical phylogenetic inference assumes independence between sites, potentially undermining the accuracy of evolutionary analyses in the presence of epistasis. Some protein language models have the capacity to encode dependencies between sites in conserved structural and functional domains across the protein universe. We employ the MSA Transformer, which takes a multiple sequence alignment (MSA) as an input, and is trained with masked language modeling objectives, to investigate if and how effects of epistasis can be captured to enhance the analysis of phylogenetic relationships.
We test whether the MSA Transformer internally encodes evolutionary distances between the sequences in the MSA despite this information not being explicitly available during training. We investigate the model’s reliance on information available in columns as opposed to rows in the MSA, by systematically shuffling sequence content. We then use MSA Transformer on both natural and simulated MSAs to reconstruct entire phylogenetic trees with implied ancestral branchpoints, and assess their consistency with trees from maximum likelihood inference.
We demonstrate how both previously known and novel evolutionary relationships are available from a “non-classical” approach with very different computational requirements, by reconstructing phylogenetic trees for the RNA virus RNAdependent RNA polymerase and the nucleo-cytoplasmic large DNA virus domain. We anticipate that MSA Transformer will not replace but rather complement classical phylogenetic inference, to accurately recover the evolutionary history of protein families.
</div>