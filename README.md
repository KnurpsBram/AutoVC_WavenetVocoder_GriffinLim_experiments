# AutoVC_WavenetVocoder_GriffinLim_experiments
Experiments on AutoVC and WaveNet vocoder, compared against the Griffin Lim spectrogram inversion algorithm

Thanks to auspicious3000 for his great work on AutoVC! ([repo](https://github.com/auspicious3000/autovc), [paper](https://arxiv.org/abs/1905.05879))

AutoVC achieves zero-shot voice conversion using only a vanilla auto-encoder loss. The authors provide in their repo a pretrained model and the speaker embeddings of 4 speakers. They use a [wavenet vocoder](https://github.com/r9y9/wavenet_vocoder) to convert spectrograms to audio. 

**The pretrained models can be downloaded from [here](https://github.com/auspicious3000/autovc)**

This repo builds upon the steps of [miaoYuanyuan](https://github.com/miaoYuanyuan/gen_melSpec_from_wav), who expressed the steps of spectrogram preprocessing more clearly.

AutoVC performs Voice Conversion in three steps;
- Convert a mel-spectrogram representation of a person's speech into a mel-spectrogram representation of another person's speech (AutoVC)
- Refine the mel-spectrogram (PostNet)
- Convert a mel-spectrogram to a waveform representation (Wavenet Vocoder)

In this notebook we'll break those two steps down. We'll invert a mel-spectrogram obtained by AutoVC (with and without PostNet) with the classic Griffin-Lim algorith, and we'll invert a mel-spectrogram of real audio using the WaveNet vocoder.

The classic Griffin-Lim algorithm requires linear-scaled spectrograms (instead of mel-scaled spectrograms), therefore, we must first convert our mel spectrograms to linear scale spectrograms. As we shall see, some information (and therefore quality) gets lost in the the mel-to-linear transformation. 

```
Experiments to run (normalization steps omitted for brevity):

VC = AutoVC (requires mel spectrogram)
PN = PostNet (requires mel spectrogram)
WV = Wavenet Vocoder spectrogram inversion (requires mel spectrogram)
GL = Griffin-Lim spectrogram inversion (requires linear spectrogram)

A. aud_in --> lin_in --> mel_in -->                                                   --> WV --> aud_out 
B. aud_in --> lin_in -->                                                              --> GL --> aud_out 
C. aud_in --> lin_in --> mel_in -->                                       --> lin_out --> GL --> aud_out 

D. aud_in --> lin_in --> mel_in --> VC --> mel_out_1 --> PN --> mel_out_2 -->         --> WV --> aud_out
E. aud_in --> lin_in --> mel_in --> VC --> mel_out_1 -->                  -->         --> WV --> aud_out
F. aud_in --> lin_in --> mel_in --> VC --> mel_out_1 --> PN --> mel_out_2 --> lin_out --> GL --> aud_out
G. aud_in --> lin_in --> mel_in --> VC --> mel_out_1 -->                  --> lin_out --> GL --> aud_out

Naming:
<spkr_in>_A_lin_mel_WV.wav
<spkr_in>_B_lin_GL.wav
<spkr_in>_C_lin_mel_lin_GL.wav
<spkr_in>_D_<spkr_out>_lin_mel_VC_mel_PN_mel_WV.wav
<spkr_in>_E_<spkr_out>_lin_mel_VC_mel_WV.wav
<spkr_in>_F_<spkr_out>_lin_mel_VC_mel_PN_mel_lin_GL.wav
<spkr_in>_G_<spkr_out>_lin_mel_VC_mel_lin_GL.wav
```
