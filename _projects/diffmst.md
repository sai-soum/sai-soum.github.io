---
layout: page
title: Diff-MST - Differentiable Mixing style Transfer
description: 
importance: 1
category: work
related_publications: true

---
<div>
    Soumya Sai Vanka, Christian Steinmetz, Jean-Baptiste Rolland, Joshua D. Reiss, György Fazekas
</div>

<div style="margin:auto; text-align:center;">
 
    <h4>
        <a href="{{ '/assets/pdf/diffmst.pdf' | relative_url }}" target="_blank">paper</a> •
        <a href="https://github.com/sai-soum/Diff-MST" target="_blank">code</a> • 
        <a href="#audio-examples">audio</a>
    </h4>
</div> 
<div class="text-justify">
Diff-MST offers a novel approach to multitrack mixing style transfer, providing high-quality mixes with interpretability and controllability, and supporting real-world applications without source labeling.
</div>
## Abstract
<div class="text-justify">
    <p>
        
        Mixing style transfer automates the generation of a multitrack mix for a given set of tracks by inferring production attributes from a reference song. However, existing systems for mixing style transfer are limited in that they often operate only on a fixed number of tracks, introduce artifacts, and produce mixes in an end-to-end fashion, without grounding in traditional audio effects, prohibiting interpretability and controllability. To overcome these challenges, we introduce Diff-MST, a framework comprising a differentiable mixing console, a transformer controller, and an audio production style loss function.
        By inputting raw tracks and a reference song, our model estimates control parameters for audio effects within a differentiable mixing console, producing high-quality mixes and enabling post-hoc adjustments. Moreover, our architecture supports an arbitrary number of input tracks without source labelling, enabling real-world applications. We evaluate our model's performance against robust baselines and showcase the effectiveness of our approach, architectural design, tailored audio production style loss, and innovative training methodology for the given task. We provide code, pre-trained models, and listening examples online.
    </p>
</div>

<div class="row">
    <div style="width: 100%">
    </div>
    <div class="col-sm mt-3 mt-md-0">
        <img class="img-fluid rounded " src="{{ '/assets/img/diffmst/diffmst-main_modified.jpg' | relative_url }}" alt="" title="Architecture of DiffMST model "/>
    </div>
    <div style="width: 20%">
    </div>
</div>
<div class="caption">Diff-MST, a differentiable mixing style transfer framework featuring a differentiable multitrack mixing console, a transformer-based controller that estimates control parameters for this mixing console, and an audio production style loss function that measures the similarity between the estimated mix and reference mixes</div>

## Audio Examples

<br />
<div style="text-align: center">
<h4>trumpet</h4>
</div>
<div class="row" style="width: 100%; margin-left:0%;">
    <div style="width: 25%; text-align: center;">
        <h4>reference</h4>
        <audio controls style="width: 90%">
        <source src="{{ '/assets/audio/nws_examples/tpt1/tpt1_ref.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 25%; text-align: center;">
        <h4>ours</h4>
        <audio controls style="width: 90%">
        <source src="{{ '/assets/audio/nws_examples/tpt1/tpt1_nws.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 25%; text-align: center;">
        <h4>ours + FastNEWT</h4>
        <audio controls style="width: 90%">
        <source src="{{ '/assets/audio/nws_examples/tpt1/tpt1_nws_fn.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 25%; text-align: center;">
        <h4>DDSP-tiny</h4>
        <audio controls style="width: 90%">
        <source src="{{ '/assets/audio/nws_examples/tpt1/tpt1_ddsp_tiny.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
</div>
<div class="row" style="width: 100%; margin-left:0%;">
    <div style="width: 25%; text-align: center;">
        <audio controls style="width: 90%">
        <source src="{{ '/assets/audio/nws_examples/tpt2/tpt2_ref.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 25%; text-align: center;">
        <audio controls style="width: 90%">
        <source src="{{ '/assets/audio/nws_examples/tpt2/tpt2_nws.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 25%; text-align: center;">
        <audio controls style="width: 90%">
        <source src="{{ '/assets/audio/nws_examples/tpt2/tpt2_nws_fn.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 25%; text-align: center;">
        <audio controls style="width: 90%">
        <source src="{{ '/assets/audio/nws_examples/tpt2/tpt2_ddsp_tiny.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
</div>
<div class="row" style="width: 100%; margin-left:0%;">
    <div style="width: 25%; text-align: center;">
        <audio controls style="width: 90%">
        <source src="{{ '/assets/audio/nws_examples/tpt3/tpt1_dm_cond.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 25%; text-align: center;">
        <audio controls style="width: 90%">
        <source src="{{ '/assets/audio/nws_examples/tpt3/tpt1_dm_nws.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 25%; text-align: center;">
        <audio controls style="width: 90%">
        <source src="{{ '/assets/audio/nws_examples/tpt3/tpt1_dm_nws_fn.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 25%; text-align: center;">
        <audio controls style="width: 90%">
        <source src="{{ '/assets/audio/nws_examples/tpt3/tpt1_dm_ddsp_tiny.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
</div>
<br />