---
title: "AI-powered modeling for generative ensembles"
excerpt: "How do we generate conformational ensembles of proteins using generative AI models? <br/><a href='/research/IDPensemble/' class='image'><img src='/images/research_images/seq35.gif' width=500></a><br/>"
collection: research
category: Machine learning and deep learning
date: 2024-09-30
order: 1
---
Conventional MD can help us sample protein conformations but it is time- and resource-consuming to simulate adequately and accurately, especially for intrinsically disordered proteins (IDPs). The conformational landscape of an IDP usually has many shallow "valleys" (states), and ideally one expects to sample all conformational states as well as the transitions between them.

As you can imagine, IDPs are flexible and plastic and can adopt far more conformational states than folded proteins (where usually only a few significant states account for function). This poses significant challenges: we need to run simulations long enough to adequately sample the conformational landscape and capture enough transitions between different states (so that we can derive the hidden free energy landscape). This is extremely challenging since it requires not only an accurate and well-benchmarked IDP force field (which is very hard and an active area of ongoing improvement), but also extremely long simulations to populate all possible states, not to mention the transitions between them (and their relative probability densities).

In this research direction, we propose to use deep learning neural networks to learn from large MD simulation datasets of IDPs and efficiently generate IDP ensembles with ensemble properties comparable to MD ensembles. This can potentially avoid running extremely long simulations and enable predictive generation of IDP ensembles for any given sequence.

<img src='/images/research_images/seq35.gif' width=800>

The above GIF shows the first phase of our AI model — an autoencoder that reconstructs MD coordinates. As shown in the 3D figure, each conformation can be almost perfectly reconstructed. We used dihedral angle loss and distance map loss during training. The decoder of the autoencoder takes a latent space representation and generates 3D coordinates. The second phase of the AI model learns the latent space probability distributions of IDPs using the DDPM (denoising diffusion probabilistic model) architecture. It learns from sequences and their encoder-derived latent space representations during training. At inference time, the DDPM generates new latent space representations for a given sequence, which are then decoded into 3D coordinates. By generating enough frames, we can produce an AI-generated IDP ensemble that hopefully captures the properties of a real MD ensemble, including Rg, secondary structure, and other ensemble properties.
