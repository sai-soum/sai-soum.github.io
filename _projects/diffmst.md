---
layout: page
title: Diff-MST - Differentiable Mixing style Transfer
description: Diff-MST offers a novel approach to multitrack mixing style transfer, providing high-quality mixes with interpretability and controllability, and supporting real-world applications without source labeling.
importance: 1
category: work
related_publications: true
permalink: /projects/diffmst/
---

<div class="text-center">
    <p>
        <strong>Abstract:</strong>
        Mixing style transfer automates the generation of a multitrack mix for a given set of tracks by inferring production attributes from a reference song. However, existing systems for mixing style transfer are limited in that they often operate only on a fixed number of tracks, introduce artifacts, and produce mixes in an end-to-end fashion, without grounding in traditional audio effects, prohibiting interpretability and controllability. To overcome these challenges, we introduce \textbf{Diff-MST}, a framework comprising a differentiable mixing console, a transformer controller, and an audio production style loss function.
        By inputting raw tracks and a reference song, our model estimates control parameters for audio effects within a differentiable mixing console, producing high-quality mixes and enabling post-hoc adjustments. Moreover, our architecture supports an arbitrary number of input tracks without source labelling, enabling real-world applications. We evaluate our model's performance against robust baselines and showcase the effectiveness of our approach, architectural design, tailored audio production style loss, and innovative training methodology for the given task. We provide code, pre-trained models, and listening examples online.
    </p>



</div class = "figure-caption">
    <p>
        <strong>Figure 1:</strong> Overview of the Diff-MST framework. Given raw tracks and a reference song, our model estimates control parameters for audio effects within a differentiable mixing console, producing high-quality mixes and enabling post-hoc adjustments.
    </p>
