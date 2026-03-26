# Run Step By Step

이 문서는 Python/PyTorch를 잘 모르는 사람도 그대로 따라할 수 있게 작성한 실행 가이드입니다.

## 중요 안내

이 문서에 적힌 모든 경로는 예시입니다.  
반드시 사용자 본인 컴퓨터의 실제 경로에 맞게 수정해서 사용해야 합니다.

## 1. 어떤 파일을 실행하나요?

메인 실행 파일은 아래입니다.

- [Experiments_Neuron.py](../Experiments_Neuron.py)

이 파일이 다음 작업을 모두 담당합니다.

- 실험 설정
- 모델 초기화
- 데이터 로딩
- 학습
- 검증
- 테스트
- 결과 그래프 출력

## 2. 실행 전에 수정해야 하는 것

### 가장 먼저: 데이터 경로 수정

파일 상단 근처에 아래 코드가 있습니다.

```python
des_path = "/path/to/your_dataset_root"
```

이 값을 본인 컴퓨터의 실제 데이터 폴더 경로로 바꿔야 합니다.

예를 들어 데이터가 아래 경로에 있다면

```text
/path/to/my_dataset/Neuron_AST_optics
```

코드도 이렇게 바꿔야 합니다.

```python
des_path = "/path/to/my_dataset/Neuron_AST_optics"
```

## 3. 그 외 주요 설정값

메인 파일에는 아래 실험 설정이 있습니다.

### 모델 종류

```python
args.model_name = "resnet"
```

의미:

- 어떤 모델 구조를 사용할지 정합니다.
- 현재 기본값은 `resnet`입니다.

### 클래스 수

```python
args.num_classes = 2
```

의미:

- 분류해야 하는 클래스 개수입니다.
- 데이터 클래스가 2개면 그대로 두면 됩니다.

### 배치 크기

```python
args.batch_size = 64
```

의미:

- 한 번에 몇 장의 이미지를 묶어서 학습할지 정합니다.

### epoch 수

```python
args.num_epochs = 200
```

의미:

- 전체 데이터를 몇 번 반복해서 학습할지 정합니다.

### feature extract 여부

```python
args.feature_extract = False
```

의미:

- `False`: 모델 전체를 학습
- `True`: 일부만 학습

## 4. 실제 실행 순서

### Step 1. 프로젝트 폴더 열기

터미널 또는 명령 프롬프트에서 프로젝트 폴더로 이동합니다.

```bash
cd /path/to/Neuron_AST_classification_code
```

Windows라면 본인 경로 기준으로 이동하면 됩니다.

### Step 2. `Experiments_Neuron.py` 수정 저장

최소한 아래를 확인합니다.

- `des_path`
- `num_classes`
- `model_name`
- `batch_size`
- `num_epochs`

### Step 3. 실행

```bash
python Experiments_Neuron.py
```

## 5. 실행하면 처음에 무엇이 보이나요?

정상이라면 대략 아래 흐름이 보입니다.

- PyTorch 버전 출력
- Torchvision 버전 출력
- 모델 구조 출력
- `Initializing Datasets and Dataloaders...`
- 이미지 시각화 창
- 학습 epoch 로그

예를 들어 다음과 비슷한 문장이 보일 수 있습니다.

```text
PyTorch Version: ...
Torchvision Version: ...
Initializing Datasets and Dataloaders...
Epoch 0/199
train Loss: ...
train Acc: ...
val Loss: ...
val Acc: ...
```

## 6. 정상 실행 중이라는 신호

아래가 보이면 대체로 정상입니다.

- 에러 없이 epoch가 계속 진행됨
- `train Loss`, `val Loss`, `train Acc`, `val Acc`가 출력됨
- 마지막에 `test Loss`, `test Acc`가 출력됨
- 그래프 창이 뜸

## 7. 자주 발생하는 문제와 확인 방법

### 문제 1. 파일 경로 에러

확인:

- `des_path`가 실제 데이터 루트를 가리키는가
- 그 아래에 `train/val/test`가 있는가

### 문제 2. 클래스 수 오류

확인:

- 실제 클래스 폴더 개수와 `args.num_classes`가 맞는가

### 문제 3. 실행은 되지만 매우 느림

확인:

- GPU를 쓰는 환경인지
- batch size가 너무 큰지

### 문제 4. 그래프가 뜨지 않음

확인:

- 실행 환경에서 matplotlib 창이 열 수 있는지
- 코드가 에러 없이 마지막까지 갔는지

## 8. 실행이 끝나면 무엇을 봐야 하나요?

실행이 끝난 후에는 아래를 봅니다.

- 마지막 `test Acc`
- 마지막 `test Loss`
- train/val accuracy 그래프
- train/val loss 그래프

그리고 아래 질문에 답해봅니다.

- 정확도가 충분히 높은가?
- train은 높은데 val/test는 낮은가?
- loss가 이상하게 증가하는가?

이 해석은 `docs/05_expected_results.md`에서 더 자세히 설명합니다.
