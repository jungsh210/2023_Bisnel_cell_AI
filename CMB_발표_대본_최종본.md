# 🎤 CMB 검출 연구 포스터 발표 대본 (섹션별 카드형)

> 2026 의공학회 춘계 포스터 발표용
> 발표자: 정수현 (인제대학교 디지털항노화헬스케어학과)
> 분량: 약 7~8분
> **활용법**: 발표 중 한 섹션씩 보면서 자연스럽게 설명

---

# 🔬 연구 방법 한눈에 (발표 전 마지막 점검용)

본 연구에서 사용한 모든 방법을 명확히 정리합니다.

| 카테고리 | 사용한 방법 |
|---|---|
| **데이터 전처리** | HD-BET 두개골 제거 / N4 bias field correction (ANTsPy) / 16-bit PNG 변환 / LMDB 저장 |
| **자동 GT 생성** | **OpenCV cv2.findContours** (윤곽선 추출) + **cv2.boundingRect** (axis-aligned bbox 생성) |
| **데이터 분할** | **Patient-level stratified split** / 6개 strata (병변 개수 기반) / extreme case는 항상 train |
| **Baseline 모델** | **SSD-FE** = SSD + Feature Enhancement / VGG-16 (ImageNet pretrained) backbone + extra deep layers / Detection heads at conv4_3 ~ conv9_2 |
| **FE 모듈** | Train-only / GT bbox 안 어두운 픽셀 feature 증폭 / 학습 파라미터 없음 / 추론 시 pass-through |
| **제안 방법 (1)** | **Shallow detection heads**: conv2 (1/2 scale) + conv3 (1/4 scale) 5개 sub-layer 사용 |
| **제안 방법 (2)** | **Confidence threshold sweep**: 0.0001 ~ 0.95 (23개 값) → 0.02 선택 |
| **제안 방법 (3)** | **NMS IoU sweep**: 1e-6 ~ 0.9 → 0.05 선택 |
| **Loss function** | MultiBox Loss = Classification (CE) + Localization (Smooth L1) / **Hard negative mining** (3:1) / **Brain ROI filtering** (anchor 중심점 brain 안인지 확인) |
| **학습 방법** | Adam optimizer / Mixed precision (AMP) / Gradient clipping (max_norm=1.0) / LR schedule (1e-3 → 1e-4 → 1e-5) |
| **데이터 증강** | Kornia AugmentationSequential / 이미지+mask 동시 변환 |
| **평가 protocol 1** | **Fixed test set** (82명/834슬라이스/516병변) — hyperparameter 선택용 |
| **평가 protocol 2** | **10-fold cross-validation** (fold당 평균 27명) — 일반화 검증용 |
| **평가 지표** | Precision, Recall, F1, AP@0.001, FP/CMB / **Task-specific IoU threshold 0.001** |

---

# 📋 발표 흐름 한눈에

| # | 섹션 | 시간 | 무엇을 가리킴 |
|---|---|---|---|
| 0 | 시작 인사 | 0:20 | (정면) |
| 1 | 연구 배경 | 0:55 | Introduction |
| 2 | 기존 연구 + 한계 | 0:55 | (없음, 흐름) |
| 3 | 연구 목표 + 자동 GT 방법 | 0:50 | (없음, 흐름) |
| 4 | 새로운 문제 | 1:00 | (없음, 흐름) |
| 5 | 제안 방법 (3가지) | 1:30 | Fig 1 |
| 6 | 결과 - Fig 2 | 0:40 | Fig 2 |
| 7 | 결과 - Fig 3 | 0:30 | Fig 3 |
| 8 | 결과 - Table 1 | 1:00 | Table 1 |
| 9 | 결론 + 의의 | 0:50 | (정면) |
| 10 | 마무리 | 0:15 | (정면) |

**총 약 7~8분**

---

---

# 🎯 섹션 0: 시작 인사

> **⏱ 시간**: 약 20초
> **👁 시선**: 정면, 청중 응시

## 📢 발표 내용

> 안녕하십니까.
> 인제대학교 **디지털항노화헬스케어학과 정수현**입니다.
>
> 오늘 발표드릴 연구는
> **"ROI 마스크 기반 자동 ground truth 생성과 후처리 최적화를 통한 뇌미세출혈 검출 성능 향상"**입니다.
>
> 본 연구는 **인제대학교, 세종대학교, 삼성서울병원, 건국대학교병원과의 공동 연구**로 진행되었으며,
> **건국대학교병원에서 제공한 SWI 및 ROI 데이터**를 활용하였습니다.

## 🔑 핵심 키워드
- 디지털항노화헬스케어학과
- 4개 기관 공동 연구
- 건국대학교병원 데이터

---

---

# 🎯 섹션 1: 연구 배경 — CMB 소개

> **⏱ 시간**: 약 55초
> **👁 시선**: 청중 + 가끔 Introduction 박스

## 📢 발표 내용

> 먼저 연구 배경부터 말씀드리겠습니다.
>
> 뇌미세출혈, 즉 **CMB**는
> 대뇌의 작은 혈관에서 출혈된 혈액이 뇌 조직에 남는 병변으로,
> **뇌졸중, 인지 저하, 알츠하이머** 등과 관련된 **중요한 임상 지표**입니다.
>
> 이 병변은 **SWI MRI 영상에서 어두운 점 형태**로 나타납니다.
>
> 여기서 한 가지 짚고 넘어가겠습니다.
> **SWI는 자화율 강조 영상**으로,
> 출혈 부위의 성분이 자기장에 영향을 주어 신호가 강하게 감소하면서
> 주변 조직보다 더 어둡게 나타납니다.
> 따라서 SWI에서 **"어둡게 보인다"는 것은 이상 신호가 아니라, 임상적으로 진단의 핵심이 되는 특징**입니다.
>
> 하지만 이 CMB를 검출하는 것은 매우 어렵습니다.
>
> **첫째, 크기가 매우 작습니다.**
> 하나의 병변이 약 **2픽셀에서 5픽셀** 정도에 불과합니다.
>
> **둘째, 정상 혈관과 모양이 유사합니다.**
> SWI에서는 정상 혈관도 어둡게 보이기 때문에
> **혈관을 병변으로 잘못 검출하는 false positive가 자주 발생**합니다.

## 🔑 핵심 키워드
- 뇌졸중, 인지 저하, 알츠하이머 → 임상 지표
- SWI = 자화율 강조 영상
- 어두운 게 정상 (헤모시데린 효과)
- 2~5픽셀 / 정상 혈관과 유사

## 🔬 사용된 방법
- **데이터**: 건국대학교병원 SWI MRI + ROI mask
- **전처리 파이프라인**:
   - **HD-BET** (사전학습 딥러닝 모델 기반 두개골 제거)
   - **N4 bias field correction** (ANTsPy 라이브러리, 자기장 불균일 보정)
   - **16-bit PNG 변환** (intensity 정밀도 보존)
   - **LMDB 저장** (효율적 I/O)

## ⚠️ 자주 받는 질문 대비
> Q. "어두운 게 정상이라면 의사들은 뭘 보고 진단하나요?"
> A. **헤모시데린이 자기장에 영향**을 주어 SWI에서 강하게 감쇠된 어두운 신호 자체가 진단의 단서입니다.

---

---

# 🎯 섹션 2: 기존 연구와 한계

> **⏱ 시간**: 약 55초
> **👁 시선**: 청중

## 📢 발표 내용

> 이러한 검출의 어려움을 해결하기 위해
> 기존 연구에서는 **객체 검출 모델인 SSD에 Feature Enhancement, 즉 FE 모듈을 결합한 SSD-FE 모델**을 제안한 바 있습니다.
>
> 구체적으로는 **VGG-16을 backbone으로 사용**하고, 그 위에 deep extra layer를 쌓아서 **conv4_3부터 conv9_2까지 다섯 개의 deep feature map에 detection head를 부착**합니다.
> SSD는 **Single-Shot Detector의 약자로, 한 번의 forward pass로 다양한 크기의 객체를 검출하는 multi-scale detection 모델**입니다.
>
> 그리고 **FE 모듈은 학습 시에만 동작하는 모듈**로,
> **GT bounding box 안의 어두운 픽셀의 feature를 증폭**시켜
> detector가 **CMB의 hypointense 패턴에 더 집중하도록 유도**하는 역할을 합니다.
> 학습 가능한 파라미터는 없고, 추론 시에는 pass-through로 작동합니다.
>
> 이 SSD-FE 접근은 의미 있는 성능 향상을 보였지만,
> **두 가지 중요한 한계**가 있었습니다.
>
> **첫째**, GT bounding box를 **영상의학과 전문의가 직접 주석**해야 합니다.
> 한 환자의 수십 개 병변마다 박스를 그리는 작업은 막대한 시간과 노력을 요구합니다.
>
> **둘째**, 이로 인해 **대규모 데이터로의 확장이 어렵습니다**.
> 다기관 연구나 인구 단위 코호트 분석에 필요한 수백·수천 명 환자 데이터를 현실적으로 다루기 힘듭니다.

## 🔑 핵심 키워드
- SSD = Single-Shot Detector / multi-scale
- VGG-16 backbone + extra deep layer
- Detection heads: conv4_3 ~ conv9_2 (5개)
- FE = 학습 시 어두운 픽셀 증폭 / 파라미터 없음
- 한계 1: 수동 annotation
- 한계 2: 확장 어려움

## 🔬 사용된 방법 (Baseline)
- **모델**: SSD-FE (기존 연구)
- **Backbone**: VGG-16 (ImageNet pretrained)
- **Detection heads**: conv4_3, conv7, conv8_2, conv9_2, conv10_2 (5개 deep layer)
- **FE 모듈**: train-only feature amplification (학습 파라미터 없음)
- **Anchor 방식**: Multi-scale, 다양한 aspect ratio (1:1, 2:1, 0.5:1, 3:1, 1:3)

## ⚠️ 자주 받는 질문 대비
> Q. "FE 모듈이 정확히 어떻게 작동하나요?"
> A. 학습 시에만 동작 / GT bbox 안 어두운 픽셀 증폭 / 학습 파라미터 없음 / 추론 시 pass-through

---

---

# 🎯 섹션 3: 연구 목표 + 자동 GT 생성 방법

> **⏱ 시간**: 약 50초
> **👁 시선**: 청중

## 📢 발표 내용

> 이러한 기존 연구의 한계를 극복하기 위해,
> **본 연구의 핵심 목표는 다음과 같습니다.**
>
> 👉 **검출 성능은 유지하면서, 수작업 bounding box annotation을 제거하는 것**입니다.
>
> 즉,
> 👉 **detection 구조는 유지하면서 annotation 비용을 없애는 것**이 핵심입니다.
>
> 이를 위해
> **ROI segmentation mask로부터 bounding box를 자동 생성**했습니다.
>
> 자동 GT 생성은 **별도의 학습이나 복잡한 모델 없이 표준 영상 처리 라이브러리만으로 구현**했습니다.
>
> 구체적으로는 **OpenCV 라이브러리의 `cv2.findContours` 함수**를 이용하여
> ROI mask에서 각 lesion의 윤곽선을 connected component 단위로 추출하고,
> **`cv2.boundingRect` 함수**로 각 윤곽선을 둘러싸는 axis-aligned bounding box를 자동 생성합니다.
>
> 이렇게 생성된 박스는 ROI mask의 contour를 픽셀 단위로 정확히 따라가기 때문에
> **사람의 주관이 개입되지 않으며 일관되고 재현 가능한 GT**를 얻을 수 있습니다.

## 🔑 핵심 키워드
- 핵심: detection 구조 유지 + annotation 비용 제거
- OpenCV `cv2.findContours` + `cv2.boundingRect`
- 표준 영상 처리만 사용 / 학습 모델 X
- 일관되고 재현 가능한 GT

## 🔬 사용된 방법 (자동 GT 생성)
- **라이브러리**: OpenCV (cv2)
- **함수 1**: `cv2.findContours` — connected component 기반 윤곽선 추출
- **함수 2**: `cv2.boundingRect` — 각 윤곽선의 axis-aligned bbox 자동 계산
- **저장 형식**: JSON (`all_bboxes.json`)
- **장점**: 학습 불필요 / 재현 가능 / 일관성 보장

## ⚠️ 자주 받는 질문 대비
> Q. "자동 GT는 어떻게 생성했나요? 라이브러리 사용했나요?"
> A. **OpenCV의 findContours와 boundingRect** 사용 (이미 본문에 명시했으므로 추가 답변 불필요)

---

---

# 🎯 섹션 4: 새로운 문제 발생

> **⏱ 시간**: 약 60초
> **👁 시선**: 청중

## 📢 발표 내용

> 하지만 여기서 **중요한 문제**가 발생합니다.
>
> ROI 기반으로 생성된 bounding box는
> **기존 수작업 박스보다 훨씬 더 타이트**합니다.
>
> 여기서 **"타이트하다"**는 것은
> 👉 **병변 주변 여유 없이, 병변 영역에 거의 정확히 맞춰진다**는 의미입니다.
>
> 수작업 박스는 사람이 약간의 여유를 두고 그리는 반면,
> ROI 기반 박스는 **OpenCV의 contour 추출을 통해 픽셀 단위로 정확히 생성**되기 때문입니다.
>
> 이로 인해
> 👉 **기존 SSD-FE 모델을 그대로 적용하면**
> 👉 **거의 모든 작은 병변을 놓치게 됩니다.**
>
> 그 이유는 다음과 같습니다.
>
> 기존 SSD-FE는 **conv4 이후의 깊은 layer (conv4_3 ~ conv9_2)에서만 검출을 수행**하는데,
> 이 과정에서 **feature map 해상도가 1/8 이하로 낮아지면서**
> 작은 병변 정보가 사라집니다.
>
> 특히 **5픽셀 크기의 병변이 feature map에서는 1픽셀 이하로 줄어들어**
> 모델이 인식할 수 없게 됩니다.
>
> 수작업 박스는 약간의 여유가 있어
> **위치 오차를 어느 정도 허용**하지만,
> ROI 기반 박스는 **매우 정확한 위치 일치를 요구**하기 때문에
> 이 문제가 더욱 크게 드러납니다.

## 🔑 핵심 키워드
- ROI > 수작업: 타이트 (픽셀 단위 정확)
- 깊은 layer = 해상도 1/8 이하
- 5픽셀 → 1픽셀 미만 사라짐
- 수작업은 margin → 위치 오차 허용
- ROI는 정확한 위치 요구 → 더 가혹

## 🔬 진단된 원인
- **공간 해상도 손실**: VGG-16의 conv4 이후 layer는 입력 대비 1/8 ~ 1/64 scale
- **수용 영역(receptive field) 과다**: 깊은 layer는 RF가 너무 넓어 작은 lesion이 주변과 섞임
- **위치 정확도 요구 차이**: ROI 박스는 manual보다 IoU 기준이 가혹

## ⚠️ 자주 받는 질문 대비
> Q. "수작업도 박스 크기는 비슷할 텐데 기존 모델도 작은 병변 놓친 거 아닌가요?"
> A. **본질적 한계는 동일**하지만, 수작업 margin이 위치 오차를 허용해서 표면적으로는 어느 정도 검출됨. ROI는 그 한계를 더 가혹하게 노출.

---

---

# 🎯 섹션 5: 제안 방법 (Fig 1 참조)

> **⏱ 시간**: 약 90초
> **👁 시선**: Fig 1을 손가락으로 가리키며

## 📢 발표 내용 (Fig 1 가리키며)

> 이 문제를 해결하기 위해
> **세 가지 방향**으로 모델을 개선했습니다.
> Fig 1을 보시면 baseline과 제안 모델이 좌우로 비교되어 있습니다.

### **(1) 얕은 layer에서 검출 수행 (핵심 변경)**

> **첫 번째이자 가장 중요한 변경**은 detection head의 위치입니다.
>
> 기존 모델은 **깊은 layer (conv4_3 ~ conv9_2)에서만 검출**을 수행했지만,
> 본 연구에서는 **VGG-16의 더 얕은 layer인 conv2와 conv3에만 detection head를 배치**했습니다.
> 구체적으로는 **conv2_1, conv2_2, conv3_1, conv3_2, conv3_3 다섯 개 sub-layer 모두를 활용**합니다.
>
> 얕은 layer는 해상도가 높기 때문에
> **작은 병변의 형태 정보를 더 잘 유지**할 수 있습니다.
>
> 예를 들어,
> 5픽셀 병변이 **conv2(1/2 scale)에서는 약 2.5픽셀**,
> **conv3(1/4 scale)에서는 약 1.25픽셀**로 유지되어
> 검출이 가능해집니다.
>
> 또한 **VGG-16 backbone을 conv3 이후로 잘라서** 이후 layer는 사용하지 않습니다.
> 어차피 deep layer를 안 쓰기 때문에 계산 자원도 절약됩니다.
>
> 그리고 **anchor box의 aspect ratio도 0.875, 1.0, 1.125로 제한**했습니다. CMB가 거의 둥근 형태라 다양한 비율이 불필요하기 때문입니다.
>
> **FE 모듈은 baseline에서 그대로 유지**됩니다.

### **(2) confidence threshold 조정 — Brute-force sweep**

> 두 번째 변경은 **confidence threshold**입니다.
>
> 모델은 각 검출 결과에 대해 **해당 영역이 병변일 확률을 0과 1 사이의 값**으로 출력합니다.
> 이 값이 일정 기준 이상일 때만 검출로 인정하는데, 그 기준값을 confidence threshold라고 합니다.
>
> **기존 SSD 모델은 일반 객체 검출용으로 보통 0.5와 같은 비교적 높은 값**을 사용합니다.
> 하지만 **CMB는 매우 작아 모델이 부여하는 confidence가 전반적으로 낮은 경향**이 있어,
> 0.5로는 너무 엄격해 많은 병변을 놓치게 됩니다.
>
> 따라서 **brute-force sweep 방식으로 0.0001부터 0.95까지 23개 값을 fixed test set에서 평가**한 결과,
> 👉 **0.02에서 가장 안정적인 성능**을 보였습니다.

### **(3) NMS 최적화 — Brute-force sweep**

> 세 번째 변경은 **NMS (Non-Maximum Suppression)**입니다.
>
> NMS는 **같은 병변에 대해 여러 박스가 검출되었을 때, 가장 confidence가 높은 박스만 남기고 나머지를 제거**하는 객체 검출의 표준 후처리 기법입니다.
>
> 그런데 **얕은 layer를 사용하면 중복 검출이 더 심해집니다.**
> 그 이유는 **얕은 layer의 feature map은 해상도가 높아 anchor 박스 수가 baseline보다 약 6배 많아지기 때문**입니다.
> 같은 병변 주변의 여러 anchor에서 동시에 검출이 일어나면서 **중복 박스가 많이 생성**됩니다.
>
> 따라서 **NMS의 IoU 기준값을 1e-6부터 0.9까지 brute-force sweep**한 결과,
> 👉 **0.05를 선택**했습니다.
>
> 참고로, **confidence threshold와 NMS는 모두 후처리(post-processing) 방법**입니다.
> 모델이 학습된 후, **추론 단계에서 모델의 raw output을 정제하는 작업**이기 때문입니다.

## 🔑 핵심 키워드
- (1) 얕은 layer = conv2 (1/2) + conv3 (1/4) 5개 sub-layer
- VGG-16 conv3까지만 사용 (truncated)
- Aspect ratio 제한: 0.875, 1.0, 1.125 (CMB 둥근 형태)
- (2) confidence: brute-force sweep [0.0001, 0.95] 23개 → 0.02
- 기존 SSD는 0.5 / CMB는 confidence 낮음
- (3) NMS = 중복 제거 표준
- 얕은 layer = anchor 6배 → 중복 더 심함
- NMS sweep [1e-6, 0.9] → 0.05
- confidence/NMS = 후처리

## 🔬 사용된 방법 (제안 모델)
- **Detection heads (수정)**: conv2_1, conv2_2, conv3_1, conv3_2, conv3_3 (5개 shallow layer)
- **Backbone 수정**: VGG-16, conv3 이후 layer 제거
- **Anchor 수정**: aspect ratio 0.875~1.125 (CMB 형태 반영) / 약 14만 7천 개 anchor
- **Confidence sweep**: 0.0001 ~ 0.95 (23개 값) → **0.02**
- **NMS IoU sweep**: 1e-6 ~ 0.9 → **0.05**
- **Sweep 방법**: Brute-force (모든 값 시도)
- **Sweep 평가 데이터**: Fixed test set (82명)
- **변경 안 함**: FE 모듈, MultiBox Loss, Hard negative mining, Brain ROI filtering

---

---

# 🎯 섹션 6: 결과 — Fig 2 (Layer Ablation)

> **⏱ 시간**: 약 40초
> **👁 시선**: Fig 2 (bar chart) 가리키며

## 📢 발표 내용 (Fig 2 가리키며)

> Fig 2는 **layer 변경에 따른 검출 성능의 변화**를 보여주는 막대 그래프입니다.
> **fixed test set** 기준입니다.
>
> 가로축에는 4가지 configuration이 왼쪽부터 차례로 있습니다:
> **Base, Conv3, Conv2, 그리고 Conv2+Conv3**입니다.
>
> 결과를 보시면, **얕은 layer로 갈수록 검출률(recall)이 크게 증가**합니다.
>
> 구체적인 수치로 말씀드리면:
> - **Base**: Recall **0.359** — 65%의 병변을 놓침
> - **Conv3**만: Recall **0.824**
> - **Conv2**만: Recall **0.981**
> - **Conv2+Conv3**: Recall **0.992** — 거의 모든 병변 검출
>
> 다만 **빨간 선의 FP/CMB도 함께 증가**하는데,
> 이는 앞서 말씀드린 **얕은 layer의 anchor 다수에서 같은 lesion을 중복 검출**했기 때문이며,
> **이 문제는 NMS 최적화로 해결**합니다.

## 🔑 핵심 수치 (외울 것)
| Config | Recall |
|---|---|
| Base | **0.359** |
| Conv3 | 0.824 |
| Conv2 | 0.981 |
| **Conv2+Conv3** | **0.992** |

## 🔬 사용된 방법 (Layer Ablation)
- **Ablation 방식**: Layer 조합별로 모델을 재학습하여 비교
- **평가 데이터**: Fixed test set (82명, 834슬라이스, 516병변)
- **비교한 configuration**: Base / Conv3 only / Conv2 only / Conv2+Conv3
- **평가 지표**: Recall, AP@0.001, FP/CMB

---

---

# 🎯 섹션 7: 결과 — Fig 3 (Visual Comparison)

> **⏱ 시간**: 약 30초
> **👁 시선**: Fig 3 (3-panel 이미지) 가리키며

## 📢 발표 내용 (Fig 3 가리키며)

> Fig 3는 **실제 검출 결과의 시각적 비교**입니다.
> 세 개의 panel로 구성되어 있습니다.
>
> **왼쪽 panel은 "Automatic GT (reference)"**, ROI mask로부터 자동 생성된 GT 박스 위치를 빨간 박스로 표시합니다.
>
> **가운데 panel은 "Base (missed)"**, baseline 검출 결과입니다.
> 빨간 글씨 **"missed"**로 표시되어 있는데, 이는 baseline이 이 작은 lesion을 **놓쳤다**는 뜻입니다.
> baseline이 deep layer만 사용해서 작은 lesion이 feature map에서 사라졌기 때문입니다.
>
> **오른쪽 panel은 "conv2 + conv3 + NMS (detected)"**, 우리 제안 모델의 결과입니다.
> 녹색 글씨 **"detected"**로 표시되어 있고,
> 검출된 박스 옆에 **0.98**이라는 confidence score도 함께 표시되어 있습니다.
> **즉, 같은 lesion을 매우 높은 신뢰도로 정확히 검출**한 것입니다.

## 🔑 핵심 키워드
- 왼쪽: Automatic GT (reference) — 빨간 박스
- 가운데: Base (missed) — 놓침
- 오른쪽: Proposed (detected) — confidence 0.98

---

---

# 🎯 섹션 8: 결과 — Table 1 (단계별 누적 성능)

> **⏱ 시간**: 약 60초
> **👁 시선**: Table 1 가리키며 (행을 위에서 아래로)

## 📢 발표 내용 (Table 1 가리키며)

> Table 1은 **단계별 최적화의 누적 효과**를 정리한 표입니다.
> 5개 행으로 구성되어 있습니다. 위에서부터 차례로 보겠습니다.

### 1행
> **첫 번째 행, Baseline (Base SSD-FE)**:
> Precision **0.889**, Recall **0.359**, F1 **0.511**, AP 0.340, FP/CMB 0.045
> **Precision은 높은데 recall이 낮아서 F1이 0.51 수준**에 머무릅니다. baseline 자체가 작은 lesion을 잡지 못한다는 뜻입니다.

### 2행
> **두 번째 행, Confidence Tuning (0.02)**:
> Precision 0.930, Recall 0.306, F1 0.461
> Precision은 살짝 올랐지만 **recall은 오히려 떨어졌습니다**. 이는 **baseline 자체의 한계**이지 threshold 문제가 아니라는 걸 보여줍니다.

### 3행 (큰 변화)
> **세 번째 행, Shallow Feature Optimization (conv2 + conv3)**:
> 여기서 큰 변화가 일어납니다.
> Precision 0.723, **Recall 0.992**, F1 0.837, **AP 0.987**
> **Recall이 0.359에서 0.992로 급상승**했습니다. 다만 precision이 0.723으로 일시적으로 낮아졌습니다.

### 4행 (회복)
> **네 번째 행, NMS Optimization (0.05)**:
> **Precision 0.985**, Recall 0.992, F1 **0.988**, FP/CMB **0.010**
> NMS 강화로 중복이 제거되어 **precision이 0.723에서 0.985로 회복**되었고, **recall 손실은 전혀 없습니다**.

### 5행 (최종 강조)
> **마지막 다섯 번째 행, Final Model (10-fold cross-validation 평균)**:
> 👉 **F1 = 0.992 ± 0.009, FP/CMB = 0.008 ± 0.018**
>
> **표준편차가 모두 0.02 미만**이라는 점에 주목해 주십시오.
> 이는 fold 간 결과가 매우 일관되고, **특정 환자에 과적합되지 않은 안정적인 모델**임을 의미합니다.

## 🔑 핵심 수치 (외울 것)
| Row | Setting | Precision | Recall | F1 |
|---|---|---|---|---|
| 1 | Baseline | 0.889 | 0.359 | 0.511 |
| 3 | Shallow | 0.723 | 0.992 | 0.837 |
| 4 | + NMS | **0.985** | 0.992 | **0.988** |
| 5 | **10-fold** | **0.992 ± 0.017** | **0.992 ± 0.009** | **0.992 ± 0.009** |

## 🔬 사용된 평가 방법
- **Stepwise ablation**: 각 단계 적용 후 누적 성능 측정
- **Fixed test set**: 1~4행 (hyperparameter 선택용)
- **10-fold cross-validation**: 5행 (일반화 검증용)
- **Patient-level stratified split**: 환자 단위 분할 + 6개 strata (병변 개수 기준)
- **평가 지표**: Precision, Recall, F1, AP@0.001, FP/CMB
- **Task-specific IoU threshold**: 0.001 (CMB 크기 고려)

## 💡 추가 설명 (시간 여유 있을 때)
> 참고로, **각 단계별 hyperparameter sweep은 fixed test set에서 수행했고, 최종 평가는 10-fold cross-validation에서 진행**했습니다.
> 이는 **fixed test set이 hyperparameter 선택용이고, 10-fold CV는 일반화 검증용**으로 역할이 다르기 때문입니다.
> 두 split은 같은 환자 풀에서 독립적으로 random split되었으며, **표준편차 0.009**는 일반화가 잘 됨을 보여줍니다.

---

---

# 🎯 섹션 9: 결론 + 의의

> **⏱ 시간**: 약 50초
> **👁 시선**: 정면, 청중 응시 (자신감 있게)

## 📢 발표 내용

### 결론
> 정리하겠습니다.
>
> 본 연구는
> **OpenCV 기반 영상 처리(`cv2.findContours`, `cv2.boundingRect`)로 ROI 마스크에서 bounding box를 자동 생성**함으로써
> **수작업 annotation을 완전히 제거**하였습니다.
>
> 그리고 이에 맞게 **detection head를 conv2와 conv3 얕은 layer로 옮기고, confidence threshold와 NMS IoU를 brute-force sweep으로 최적화**하여
> **검출 성능을 임상 등급 수준으로 유지**할 수 있음을 확인했습니다.
>
> 👉 결국 이 연구의 핵심은
> **detection 구조를 유지하면서 annotation 비용을 제거하고, 그에 따른 검출 실패 문제까지 해결**한 것입니다.

### 의의
> 본 연구의 의의는 두 가지입니다.
>
> **첫째**, 영상의학과 전문의의 수작업 annotation 부담을 제거하여 **대규모 데이터 분석을 가능하게** 합니다.
> 1,000명 환자의 데이터에 박스를 그리려면 전문의가 수백 시간을 투입해야 하지만, **자동화되면 수천에서 수만 명 규모의 데이터도 빠르게 처리 가능**해집니다.
> 이로써 **인구 단위의 CMB 역학 연구나 임상 시험에서의 영상 분석이 현실적으로 가능**해지는 것입니다.
>
> **둘째**, 이 접근 방식은 **CMB뿐만 아니라 lacunar infarct이나 micro metastasis 같은 다른 작은 병변 검출 과제**에도 일반화 적용 가능합니다.
> ROI segmentation 주석은 가능하지만 bounding box가 없는 모든 의료영상 도메인에 응용될 수 있습니다.

## 🔑 핵심 키워드
- 핵심: detection 구조 유지 + annotation 비용 제거 + 검출 실패 해결
- 의의 1: 수만 명 규모 분석 가능 / 인구 단위 연구
- 의의 2: lacunar infarct, micro metastasis 일반화 가능

## 🔬 본 연구의 종합 contribution
1. **OpenCV 기반 자동 GT 생성** (수작업 annotation 제거)
2. **Shallow detection heads (conv2, conv3)** (작은 lesion 검출 회복)
3. **Brute-force sweep으로 confidence threshold (0.02), NMS IoU (0.05) 최적화**
4. **10-fold CV로 일반화 검증** (F1 0.992 ± 0.009)

---

---

# 🎯 섹션 10: 마무리

> **⏱ 시간**: 약 15초
> **👁 시선**: 정면, 가벼운 미소

## 📢 발표 내용

> 향후에는 **다양한 병원 데이터를 활용한 다기관 검증(multi-center validation)**을 통해
> 모델의 일반화 성능을 추가로 검증할 계획입니다.
>
> 이상으로 발표를 마치겠습니다.
> 경청해 주셔서 감사합니다.

## 🔑 핵심 키워드
- 다기관 검증 = future work
- 가벼운 인사로 마무리

---

---

# 📊 발표 시 참조할 핵심 수치 (전체 모음)

## Fig 2 — Layer Ablation (fixed test set)

| Configuration | Recall | AP@0.001 | FP/CMB |
|---|---|---|---|
| Base | 0.359 | 0.340 | 0.045 |
| Conv3 | 0.824 | 0.817 | 0.072 |
| Conv2 | 0.981 | 0.960 | 0.180 |
| **Conv2+Conv3** | **0.992** | **0.987** | 0.380 |

## Table 1 — Stepwise Performance

| Setting | Configuration | Precision | Recall | F1 | AP@0.001 | FP/CMB |
|---|---|---|---|---|---|---|
| Baseline | Base SSD-FE | 0.889 | 0.359 | 0.511 | 0.340 | 0.045 |
| Confidence Tuning | 0.02 | 0.930 | 0.306 | 0.461 | 0.291 | 0.021 |
| Shallow Feature | conv2 + conv3 | 0.723 | 0.992 | 0.837 | 0.987 | 0.380 |
| NMS Optimization | 0.05 | 0.985 | 0.992 | 0.988 | 0.990 | 0.010 |
| **Final Model** | **10-fold avg** | **0.992 ± 0.017** | **0.992 ± 0.009** | **0.992 ± 0.009** | **0.991 ± 0.009** | **0.008 ± 0.018** |

---

# ⚡ 응급 상황 대응

## 시간 부족 시 (3분 단축 버전)

> "안녕하십니까, 인제대 정수현입니다. 본 연구는 **뇌미세출혈, CMB 검출 성능 향상**을 다룹니다.
>
> CMB는 2~5픽셀의 매우 작은 병변이라 검출이 어렵고, 기존 SSD-FE 방법은 영상의학과 전문의의 수동 주석에 의존해 확장이 어렵습니다.
>
> 저희는 **OpenCV의 findContours와 boundingRect를 사용해 ROI 마스크에서 GT를 자동 생성**해 이 문제를 해결했습니다. 그러나 ROI 기반 박스가 너무 타이트해서 default SSD-FE 검출기로는 작은 lesion을 놓치는 새 문제가 발생했습니다.
>
> 이를 해결하기 위해 **(1) detection head를 conv2와 conv3 shallow layer로 이동, (2) confidence threshold를 brute-force sweep으로 0.02로 튜닝, (3) NMS IoU를 sweep으로 0.05로 튜닝**의 세 가지 최적화를 수행했고,
>
> 결과적으로 10-fold cross-validation에서 **F1 = 0.992 ± 0.009, FP/CMB = 0.008 ± 0.018**을 달성했습니다.
>
> 이 접근은 수동 주석 없이도 임상 등급 정확도를 가능하게 하며, 다른 작은 병변 검출 task에도 일반화 적용 가능합니다.
>
> 감사합니다."

## 갑자기 떨릴 때

- 한 호흡 깊게 들이쉬고
- "잠시만요, 다시 말씀드리겠습니다" 한 마디 후 차분히 재시작
- Fig 1을 가리키며 시작하면 자연스럽게 흐름 회복

## 모르는 질문이 들어왔을 때

- 솔직히: "좋은 질문이십니다. 그 부분은 추가 검토 후 답변 드릴 수 있도록 하겠습니다."
- 추측하지 말 것 — 학술 발표에서 정직함이 가장 중요

---

# ✅ 발표 직전 체크리스트

| 항목 | 체크 |
|---|---|
| 핵심 수치 외우기 (0.359 → 0.992, F1 0.992 ± 0.009 등) | ☐ |
| Fig 1, Fig 2, Fig 3, Table 1 위치/내용 숙지 | ☐ |
| 자동 GT 생성 방법 (OpenCV findContours, boundingRect) 한 줄 외우기 | ☐ |
| Sweep 방법 (brute-force, 23개 값) 인지 | ☐ |
| Layer 정보 (conv2_1~conv3_3, 5개 sub-layer) 인지 | ☐ |
| 7분 정상 / 3분 단축 두 가지 버전 미리 연습 | ☐ |
| 거울 보고 손짓/시선 연습 | ☐ |
| 명함 준비 (관심자에게 줄 수 있도록) | ☐ |
| 발표복 + 명찰 + 포스터 출력본 백업 | ☐ |
| Q&A 답변 외우기 (별도 파일 참조) | ☐ |
| 노트북/태블릿에 백업본 준비 | ☐ |
| 가능하면 발표 전 세션장 답사 | ☐ |

---

발표 정말 잘 하시기를 응원합니다! 🎤✨
