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
<br />
<div style="margin:auto">
 
    <h4>
        <a href="{{ '/assets/pdf/diffmst.pdf' | relative_url }}" target="_blank">paper</a> •
        <a href="https://github.com/sai-soum/Diff-MST" target="_blank">code</a> • 
        <a href="#audio-examples">audio</a>
    </h4>
</div> 
<br />
<!-- <div class="text-justify">
Diff-MST offers a novel approach to multitrack mixing style transfer, providing high-quality mixes with interpretability and controllability, and supporting real-world applications without source labeling.
</div> -->

## Abstract

<div class="text-justify">
    <p>
        
        Mixing style transfer automates the generation of a multitrack mix for a given set of tracks by inferring production attributes from a reference song. However, existing systems for mixing style transfer are limited in that they often operate only on a fixed number of tracks, introduce artifacts, and produce mixes in an end-to-end fashion, without grounding in traditional audio effects, prohibiting interpretability and controllability. To overcome these challenges, we introduce <b>Diff-MST</b>, a framework comprising a differentiable mixing console, a transformer controller, and an audio production style loss function.
        By inputting raw tracks and a reference song, our model estimates control parameters for audio effects within a differentiable mixing console, producing high-quality mixes and enabling post-hoc adjustments. Moreover, our architecture supports an arbitrary number of input tracks without source labelling, enabling real-world applications. We evaluate our model's performance against robust baselines and showcase the effectiveness of our approach, architectural design, tailored audio production style loss, and innovative training methodology for the given task. We provide code, pre-trained models, and listening examples online.
    </p>
</div>

## Architecture
<div class="row justify-content-center">
    <div class="col-sm mt-3 mt-md-0 text-center">
        <img class="img-fluid rounded" src="{{ '/assets/img/diffmst/diffmst-main_modified.jpg' | relative_url }}" alt="" title="Architecture of DiffMST model" />
    </div>
</div>
<div class="row justify-content-center">
    <div class="col-sm text-center">
        <div class="caption">Diff-MST, a differentiable mixing style transfer framework featuring a differentiable multitrack mixing console, a transformer-based controller that estimates control parameters for this mixing console, and an audio production style loss function that measures the similarity between the estimated mix and reference mixes</div>
    </div>
</div>

<br />


### Differentiable Mixing Style Transfer
<div class="text-justify">
    <p>
    We propose a differentiable mixing style transfer system (Diff-MST) that takes raw tracks and a reference mix as input and predicts mixing console parameters and a mix as output. Our system employs two encoders, one to capture a representation of the input tracks and another to capture elements of the mixing style from the reference. A transformer-based controller network analyses representations from both encoders to predict the differentiable mixing console (DMC) parameters. The DMC generates a mix for the input tracks using the predicted parameters in the style of the given reference song. Given that our system oversees the operations of the DMC rather than directly predicting the mixed audio, we circumvent potential artefacts that may arise from neural audio generation techniques. This also creates an opportunity for further fine-tuning and control by the user.
    </p>
</div>
<br />

### Differetiable Mixing Console (DMC)
<div class="text-justify">
    <p>
    Multitrack mixing involves applying a series of audio effects, termed a channel strip, to each channel of a mixing console. These effects are utilized by audio engineers to address various issues such as masking, source balance, and noise. In our approach, we propose a differentiable mixing console (DMC) that integrates prior knowledge of signal processing. The DMC applies a sequence of audio effects including gain, parametric equalizer (EQ), dynamic range compressor (DRC), and panning to individual tracks, resulting in wet tracks. These wet tracks are then combined on a master bus where stereo EQ and DRC are applied to produce a mastered mix. Incorporating a master bus in the console facilitates workflow optimization, as mastered songs commonly serve as references. To enable gradient descent and training within a deep learning framework, the mixing console must be differentiable. We achieve this by utilizing differentiable effects from the <a href="https://github.com/csteinmetz1/dasp-pytorch/tree/main">dasp-pytorch</a> library.
    </p>
</div>
<div class="row justify-content-center">
    <div class="col-sm mt-3 mt-md-0 text-center">
        <img class="img-fluid rounded" src="{{ '/assets/img/diffmst/diffmst-dmc.jpg' | relative_url }}" alt="" title="Differentiable Mixing Console" />
    </div>
</div>
<div class="row justify-content-center">
    <div class="col-sm text-center">
        <div class="caption">Pipeline of Differentiable Mixing Console</div>
    </div>
</div>
<br />

## Audio Production Style Loss

<div>
    <p>The style of a mix can be broadly captured using features that describe its dynamics, spatialization, and spectral attributes (<a href="https://statisticsbyjim.com/basics/correlations/">Vanka et al., 2024</a>). Two different losses are proposed to train and optimize the models.</p>
    <p><strong>Audio Feature (AF) loss:</strong> This loss comprises traditional MIR audio feature transforms (Man et al., 2014). These $T$ features include the root mean square (RMS) and crest factor (CF), stereo width (SW), stereo imbalance (SI), and barkspectrum (BS) corresponding to the dynamics, spatialization, and spectral attributes, respectively. The system is optimized by calculating the weighted average of the mean squared error on the audio features that minimizes the distance between $M_p$ and $M_r$. The audio feature transforms $T$ are computed along with the weights $w$ as follows:</p>
    <ul>
        <li><strong>$T_1(\mathbf{x}) =$</strong> RMS$(\mathbf{x}) = \sqrt{\frac{1}{N}\sum_{i=1}^{N} x_i^2}$; $w_1 = 0.1$</li>
        <li><strong>$T_2(\mathbf{x}) =$</strong> CF$(\mathbf{x}) = 20 \log_{10}\left(\frac{\max(\lvert x_i \rvert)}{\text{RMS}(\mathbf{x})}\right)$; $w_2 = 0.001$</li>
        <li><strong>$T_3(\mathbf{x}) =$</strong> BS$(\mathbf{x}) = \log(\mathbf{FB}\cdot \lvert \text{STFT}(\mathbf{x}) \rvert + \epsilon)$; $w_3 = 0.1$</li>
        <li><strong>$T_4(\mathbf{x}) =$</strong> SW$(\mathbf{x}) = \frac{\frac{1}{N}\sum_{i=1}^{N}(x_{Li} - x_{Ri})^2}{\frac{1}{N}\sum_{i=1}^{N}(x_{Li} + x_{Ri})^2}$; $w_4 =1.0$</li>
        <li><strong>$T_5(\mathbf{x}) =$</strong> SI$(\mathbf{x}) = \frac{\frac{1}{N}\sum_{i=1}^{N}x_{Ri}^2 - \frac{1}{N}\sum_{i=1}^{N}x_{Li}^2}{\frac{1}{N}\sum_{i=1}^{N}x_{Ri}^2 + \frac{1}{N}\sum_{i=1}^{N}x_{Li}^2}$; $w_5 = 1.0$</li>
    </ul>
    <p>where $N$ represents the sequence length, $\mathbf{x}$ is the input tensor, FB is the filterbank matrix, STFT$(\mathbf{x})$ represents the short-time Fourier transform of $\mathbf{x}$, and $\epsilon$ is a small constant of value $10^{-8}$ added for numerical stability. The net loss is computed as:</p>
    <p>Loss$(\mathbf{M_p}, \mathbf{M_r}) = \frac{1}{2} \sum_{i=1}^{2} \sum_{j=1}^{5} w_{j} \cdot \text{MSE}\left(T_{j}(\mathbf{M_p}_i), T_{j}(\mathbf{M_r}_i\right))$</p>
    <p>where $w_j$ is the weight associated with $j^{th}$ transform $T_j$ and MSE corresponds to mean squared error.</p>
    <p><strong>MRSTFT loss:</strong> The multi-resolution short-time Fourier transform loss (Wang et al., 2019; Steinmetz et al., 2020) is the sum of $L_1$ distance between STFT of ground truth and estimated waveforms measured in both log and linear domains at multiple resolutions, with window sizes $W \in [512, 2048,8192]$ and hop sizes $H =W/2$. This is a full-reference metric meaning that the two input signals must contain the same content.</p>
</div>


## Audio Examples

We provide audio examples of the reference songs and the mixes generated by our model and other baselines. The audio examples are normalized to -16 LUFS.

<br />
<div style="text-align: center">
<h4>Song 1</h4>
</div>
<div class="row" style="width: 100%; margin-left:0%;">
    <div style="width: 20%; text-align: center;">
        <h6>reference</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Electronic/electronic-ref-16lufs.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 20%; text-align: center;">
        <h6>Equal Loudness</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Electronic/predictions/Equal Loudness.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 20%; text-align: center;">
        <h6>MST</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Electronic/predictions/MST.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
        <div style="width: 20%; text-align: center;">
        <h6>Human 1</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Electronic/predictions/Human 1.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 20%; text-align: center;">
        <h6>Human 2</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Electronic/predictions/Human 2.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <br />
    <div style="width: 20%; text-align: center;">
        <h6>DiffMST-MRSTFT-8</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Electronic/predictions/Diff-MST-MRSTFT-8.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 20%; text-align: center;">
        <h6>DiffMST-MRSTFT-16</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Electronic/predictions/Diff-MST-MRSTFT-16.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 20%; text-align: center;">
        <h6>DiffMST-MRSTFT+AF-8</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Electronic/predictions/Diff-MST-MRSTFT+AF-8.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 20%; text-align: center;">
        <h6>DiffMST-MRSTFT+AF-16</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Electronic/predictions/Diff-MST-MRSTFT+AF-16.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 20%; text-align: center;">
        <h6>DiffMST-AF-16</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Electronic/predictions/Diff-MST-AF-16.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>    
</div>
<br />
<br />
<div style="text-align: center">
<h4>Song 2</h4>
</div>
<div class="row" style="width: 100%; margin-left:0%;">
    <div style="width: 20%; text-align: center;">
        <h6>reference</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Metal/metal-ref-16lufs.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 20%; text-align: center;">
        <h6>Equal Loudness</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Metal/predictions/Equal Loudness.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 20%; text-align: center;">
        <h6>MST</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Metal/predictions/MST.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 20%; text-align: center;">
        <h6>Human 1</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Metal/predictions/Human 1.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 20%; text-align: center;">
        <h6>Human 2</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Metal/predictions/Human 2.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <br />
    <div style="width: 20%; text-align: center;">
        <h6>DiffMST-MRSTFT-8</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Metal/predictions/Diff-MST-MRSTFT-8.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 20%; text-align: center;">
        <h6>DiffMST-MRSTFT-16</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Metal/predictions/Diff-MST-MRSTFT-16.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 20%; text-align: center;">
        <h6>DiffMST-MRSTFT+AF-8</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Metal/predictions/Diff-MST-MRSTFT+AF-8.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 20%; text-align: center;">
        <h6>DiffMST-MRSTFT+AF-16</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Metal/predictions/Diff-MST-MRSTFT+AF-16.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 20%; text-align: center;">
        <h6>DiffMST-AF-16</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Metal/predictions/Diff-MST-AF-16.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    
</div>
<br />
<br />
<div style="text-align: center">
<h4>Song 3</h4>
</div>
<div class="row" style="width: 100%; margin-left:0%;">
    <div style="width: 20%; text-align: center;">
        <h6>reference</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Pop/pop-ref-16lufs.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 20%; text-align: center;">
        <h6>Equal Loudness</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Pop/predictions/Equal Loudness.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 20%; text-align: center;">
        <h6>MST</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Pop/predictions/MST.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 20%; text-align: center;">
        <h6>Human 1</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Pop/predictions/Human 1.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 20%; text-align: center;">
        <h6>Human 2</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Pop/predictions/Human 2.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <br />
    <div style="width: 20%; text-align: center;">
        <h6>DiffMST-MRSTFT-8</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Pop/predictions/Diff-MST-MRSTFT-8.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 20%; text-align: center;">
        <h6>DiffMST-MRSTFT-16</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Pop/predictions/Diff-MST-MRSTFT-16.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 20%; text-align: center;">
        <h6>DiffMST-MRSTFT+AF-8</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Pop/predictions/Diff-MST-MRSTFT+AF-8.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 20%; text-align: center;">
        <h6>DiffMST-MRSTFT+AF-16</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Pop/predictions/Diff-MST-MRSTFT+AF-16.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    <div style="width: 20%; text-align: center;">
        <h6>DiffMST-AF-16</h6>
        <audio controls style="width: 100%">
        <source src="{{ '/assets/audio/Listening Examples -Diff-MST/Pop/predictions/Diff-MST-AF-16.wav' | relative_url }}" type="audio/wav" />
        </audio>
    </div>
    
</div>
<br />
<br />