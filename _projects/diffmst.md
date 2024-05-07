---
layout: page
title: Diff-MST - Differentiable Mixing style Transfer
description: 
importance: 1
category: work
# related_publications: false

---
<style>
.example {
    color: #999999
}
</style>
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


The style of a mix can be broadly captured using features that describe its dynamics, spatialization, and spectral attributes. Two different losses are proposed to train and optimize the models.

**Audio Feature (AF) loss**: This loss comprises traditional MIR audio feature transforms. These features include the root mean square (RMS) and crest factor (CF), stereo width (SW), stereo imbalance (SI), and barkspectrum (BS) corresponding to the dynamics, spatialization, and spectral attributes, respectively. The system is optimized by calculating the weighted average of the mean squared error on the audio features that minimizes the distance between predicted mix and the reference song. For more information, refer to the paper. 

**MRSTFT loss**: The multi-resolution short-time Fourier transform loss (Wang et al., 2019; Steinmetz et al., 2020) is the sum of $$L_1$$ distance between STFT of ground truth and estimated waveforms measured in both log and linear domains at multiple resolutions, with window sizes $$W \in [512, 2048,8192]$$ and hop sizes $$H =W/2$$. This is a full-reference metric meaning that the two input signals must contain the same content.


## Training

We train our models end-to-end using two different methods. 
#### Method 1
We extend the data generation technique used in [^2] to a multi-track scenario. A $$t=10$$ sec segment is randomly sampled from input tracks, and a random mix of these input tracks is generated using random DMC parameters. The segment of the randomly mixed audio and the input tracks is split into two halves: $$M_{rA}$$ and $$M_{rB}$$ and $$T_A$$ and $$T_B$$ of $$t/2$$ secs each, respectively. The model is input with $$T_B$$ as input tracks and $$M_{rA}$$ as the reference song. The predicted mix $$M_p$$ is compared against $$M_{rB}$$ as the ground truth for backpropagation and updating of weights. Using different sections of the same song for input tracks and reference song encourages the model to focus on the mixing style while being content-invariant. This method allows the use of MRSTFT loss for optimization as we have the ground truth available. The predicted mix is loudness normalized to -16.0\,dBFS before computing the loss. We train models with 8 and 16 tracks using this method with MRSTFT loss and MRSTFT loss and AF loss fine tuning. 

<div class="row justify-content-center">
    <div class="col-sm mt-3 mt-md-0 text-center">
        <img class="img-fluid rounded" src="{{ '/assets/img/diffmst/diffmst-main_datagen.jpg' | relative_url }}" alt="" title="Training strategy for Method 1" />
    </div>
</div>
<div class="row justify-content-center">
    <div class="col-sm text-center">
        <div class="caption">Training strategy for Method 1</div>
    </div>
</div>
<br />


#### Method 2
A random number of input tracks between 4-16 for song A is sampled from a multitrack dataset, and a pre-mixed real-world mix of song B from a dataset consisting of full songs is used as the reference. The model is trained using AF loss computed between $$M_p$$ and $$M_r$$. This method also allows us to train the model without the availability of a ground truth. Unlike Method 1, this approach exposes the system to training examples more similar to real-world scenarios where the input tracks and the reference song come from a different song. However, due to the sampling, some input track and reference song combinations may not be realistic. We train a model with upto 16 tracks using this method using AF loss.



## Results
| Method          | RMS $$\downarrow $$ | CF $$\downarrow$$ | SW $$\downarrow$$ | SI $$\downarrow$$ | BS $$\downarrow$$ | AF Loss $$\downarrow$$ |
|-----------------|----------------------|--------------------|-------------------|-------------------|-------------------|------------------------|
| Equal Loudness  | 3.11                 | 0.51               | 3.16              | 0.21              | 33.3              | 33.389                 |
| MST             | 3.15                 | 0.45               | 4.64              | **0.13**          | **0.09**          | <u>0.185</u>           |
| **Diff-MST**    |                      |                    |                   |                   |                   |                        |
| MRSTFT-8        | 3.63                 | 1.44               | 1.97              | 4.29              | 0.17              | 0.379                  |
| MRSTFT-16       | 3.40                 | 0.98               | 1.91              | 1.99              | 0.19              | 0.328                  |
| MRSTFT+AF-8     | 3.12                 | 0.86               | 1.29              | 0.76              | 0.13              | 0.237                  |
| MRSTFT+AF-16    | 3.15                 | 0.43               | **0.89**          | 2.20              | 0.11              | <u>0.186</u>           |
| AF-16           | **2.39**             | **0.07**           | 1.60              | 0.97              | 0.13              | **0.168**              |
| Human 1         | 3.02                 | 0.26               | 2.05              | 0.46              | 0.17              | 0.218                  |
| Human 2         | 3.21                 | 0.14               | 3.63              | 2.29              | 0.11              | <u>0.180</u>           |



### Audio Examples
We compare the performance of our model against two human mixes, Equal loudness mix and mixing style transfer (MST) model from [^1]. We provide audio examples of the reference songs and the mixes generated by our model and other baselines. The audio examples are normalized to -16 LUFS.

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
    <br />
    <div style="width: 20%; text-align: center;">
        <h6>DiffMST-MRSTFT-8 <br /></h6>
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

### References
[^1]: Koo, Junghyun, et al. "Music mixing style transfer: A contrastive learning approach to disentangle audio effects." ICASSP 2023-2023 IEEE International Conference on Acoustics, Speech and Signal Processing (ICASSP). IEEE, 2023.
[^2]: Steinmetz, Christian J., Nicholas J. Bryan, and Joshua D. Reiss. "Style transfer of audio effects with differentiable signal processing." arXiv preprint arXiv:2207.08759 (2022).