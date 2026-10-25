# Task 1.2 — Decimation spectra

Train의 16명, 5,392개 윈도만 분석했다. Validation/test는 분석하지 않았다.
각 PDF에는 개별 센서 9채널이 3×3 패널로 배치되어 있고, 활동 6개를 색으로 구분했다.
같은 파일명의 PNG도 각 폴더에 저장했다.

| Sampling rate | PSD / naive | PSD / anti-aliased | Raw / naive | Raw / anti-aliased |
|---|---|---|---|---|
| 25 Hz | [PDF](01_channel_psd/naive/25Hz.pdf) | [PDF](01_channel_psd/anti_aliased/25Hz.pdf) | [PDF](raw/naive/25Hz.pdf) | [PDF](raw/anti_aliased/25Hz.pdf) |
| 12.5 Hz | [PDF](01_channel_psd/naive/12.5Hz.pdf) | [PDF](01_channel_psd/anti_aliased/12.5Hz.pdf) | [PDF](raw/naive/12.5Hz.pdf) | [PDF](raw/anti_aliased/12.5Hz.pdf) |
| 6.25 Hz | [PDF](01_channel_psd/naive/6.25Hz.pdf) | [PDF](01_channel_psd/anti_aliased/6.25Hz.pdf) | [PDF](raw/naive/6.25Hz.pdf) | [PDF](raw/anti_aliased/6.25Hz.pdf) |

## 계산 방법

- 각 조건을 원래 50 Hz 신호에서 직접 생성했다. 순차적으로 rate를 줄이지 않았다.
- Naive: `x[:, ::M, :]`로 샘플 선택.
- Anti-aliased: starter의 `downsample(..., method="aa")`를 사용했다.
  `scipy.signal.resample_poly(up=1, down=M, window=("kaiser", 5.0), padtype="line")`이다.
  FIR nominal cutoff는 새 Nyquist이고 유한한 transition band가 존재한다.
- 다운샘플링한 이후 PSD/Raw 처리를 적용했다. 신호 평균이나 Hann을 먼저 적용한 뒤 resampling하지 않았다.
- PSD: 각 윈도/채널 평균 제거, periodic Hann, one-sided density 정규화.
  활동 내 PSD를 선형 값으로 산술평균했다.
- Raw: 평균 제거, taper, density 정규화, one-sided bin 2배 보정 없이 `abs(rfft)**2`를 계산하고 활동별 평균.
- 두 버전 모두 원본 HAR 자체의 기존 전처리는 유지된다.
- 샘플 수는 64/32/16이고, FFT bin 간격은 세 조건 모두 0.390625 Hz이다.
- 같은 버전 안에서는 모든 rate/방식에 동일한 채널별 y축 범위를 사용했다.
- Raw의 크기는 샘플 수에 의존하므로 서로 다른 rate 사이의 높이 감소를 곧바로 신호 power 손실로 해석하면 안 된다.

## 표시된 경계와 구간

- x축은 25 Hz 그림에서 0–18 Hz, 12.5 Hz 그림에서 0–10 Hz,
  6.25 Hz 그림에서 0–4 Hz로 표시한다.
  Nyquist 경계값은 x축 눈금에도 명시한다.
- Nyquist: 25 Hz에서 12.5 Hz, 12.5 Hz에서 6.25 Hz, 6.25 Hz에서 3.125 Hz.
- 회색 구간은 새 Nyquist보다 높은 영역이다.
- 점선 곡선은 `Fs-f` 위치에 그린 첫 번째 대칭 복제다. 독립적인 고주파 성분이 아니다.
  복제는 해당 sampling rate인 Fs까지만 그렸고, 그 이후는 비워두었다.
- 원래 HAR의 20 Hz low-pass 표시는 20 Hz가 x축 범위 내부에 있을 때만 표시한다.
  현재 세 조건은 모두 표시 상한이 20 Hz보다 작으므로 관련 선과 문구를 생략했다.
- Anti-aliased 그림에는 새 Nyquist에 해당하는 추가 FIR nominal cutoff도 적었다.
  실제 필터는 brick-wall이 아니며 경계 부근 감쇠와 윈도 경계 영향이 있을 수 있다.
- 이 그림의 낮은 sampling rate에서 원래 20 Hz 필터로 인한 감소가 반드시 20 Hz에 나타난다고 주장하지 않는다.

## 검증과 재현

모든 조건의 shape, frequency grid, Nyquist와 PSD/Raw Parseval 관계를 검증했다.
원본 train 배열이 계산 중 변경되지 않았음도 해시로 확인했다.
설정과 수치 검사는 `analysis_config.json`, 활동별 스펙트럼은 `spectra.npz`에 저장했다.

```bash
conda activate dday
cd /home/swyoon/mamba_starter
python -m examples.task12_decimation --out runs/task12_reproduction
```

기본 축 상한은 rate별로 18/10/4 Hz이다. `--xmax`로 공통 고정 상한을 지정할 수 있다.
경계 주석/회색 영역은 `--no_annotations`로 제거할 수 있다.
현재 결과를 덮어쓰려면 `--overwrite`를 명시한다.
