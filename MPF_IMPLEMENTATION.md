# Multi-Model Pseudo-Boundary Fusion: Implementation Details

This note supplements Section III-B.1 and Eq. (1) of the paper. FEB-Bench
annotations follow the MPF--minimal-cleanup--SGR pipeline. For the subset of
images processed with the documented five-teacher MPF configuration, the
fixed model weights and cross-model response calibration are specified below.

## Fixed model weights

In this documented configuration, MPF combines five pretrained edge detectors
using the following fixed weights.

| Model | DexiNed | BDCN | DDN | UAED | NBED |
|---|---:|---:|---:|---:|---:|
| Weight | 2.5 | 1.2 | 1.8 | 2.2 | 2.4 |

The weights were determined before Edge2Me training by comparing the models'
boundary responses in the relevant imaging domains. The comparison focused on
three properties required by the subsequent measurement pipeline: boundary
completeness, boundary continuity, and suppression of non-target responses.
Models that satisfied these criteria more consistently were assigned larger
weights.

The pretrained models are not assumed to be statistically independent.
They are combined because their response errors are not identical: some models
better preserve weak or discontinuous interfaces, whereas others more
effectively suppress texture and non-target structures. Eq. (1) is a weighted
average of calibrated responses and does not require an independence
assumption.

## Response calibration and logit-space fusion

Different architectures may produce response maps with different spatial sizes
and confidence scales. Within this documented configuration, each response is
mapped back to the input image size and expressed on a common logit scale.
Probability outputs are converted to logits and temperature-scaled using
\(T_m=1.2\). The calibrated responses are then combined using the fixed weights
in Eq. (1), followed by a sigmoid mapping to obtain the fused pseudo-boundary
probability.

Logit-space fusion borrows and adapts the idea of combining multiple model
evidences in log-odds space from logarithmic opinion pooling. That work
provides design inspiration and a theoretical basis for this implementation;
the two methods are not claimed to be identical. Logits combine positive and
negative evidence before the final sigmoid mapping. Direct probability
averaging combines responses after compression to \([0,1]\), where differences
among highly confident predictions can be weakened near saturation.
[Heskes, 1998](https://papers.neurips.cc/paper_files/paper/1997/hash/59f51fd6937412b7e56ded1ea2470c25-Abstract.html).

Within the same documented configuration, a pixel-wise confidence factor is
computed from the normalized entropy after temperature scaling (gamma=2.2).
This factor and the fixed model weight jointly determine each calibrated
response's contribution; the normalized weighted result is mapped through a
sigmoid. The calibration and confidence weighting standardize the fusion
representation without adding an architecture-specific learned calibration
network.
