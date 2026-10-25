# Task 1.2b — body_acc_x

[Original 50 Hz: HAR vs HAR + 18 Hz tone](original/50Hz.pdf)

| Experiment | 25 Hz | 12.5 Hz | 6.25 Hz |
|---|---|---|---|
| Naive decimation | [PDF](naive/25Hz.pdf) | [PDF](naive/12.5Hz.pdf) | [PDF](naive/6.25Hz.pdf) |
| Anti-aliased decimation | [PDF](anti_aliased/25Hz.pdf) | [PDF](anti_aliased/12.5Hz.pdf) | [PDF](anti_aliased/6.25Hz.pdf) |
| Naive → 50 Hz interpolation | [PDF](upsampled_naive/25Hz.pdf) | [PDF](upsampled_naive/12.5Hz.pdf) | [PDF](upsampled_naive/6.25Hz.pdf) |
| Naive vs anti-aliased | [PDF](comparison_naive_vs_aa/25Hz.pdf) | [PDF](comparison_naive_vs_aa/12.5Hz.pdf) | [PDF](comparison_naive_vs_aa/6.25Hz.pdf) |
| Original vs reconstructed | [PDF](comparison_original_vs_reconstructed/25Hz.pdf) | [PDF](comparison_original_vs_reconstructed/12.5Hz.pdf) | [PDF](comparison_original_vs_reconstructed/6.25Hz.pdf) |

[Tone-only control](../controls/tone_only_verification.pdf)

Each PDF has a matching PNG. Raw |FFT|² only; fixed tone amplitude 4, phase 0.
Solid/dashed line definitions are printed on each figure.
