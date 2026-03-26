# 5분 구조 요약

이 문서는 `Neuron_AST_classification_code`를 처음 보는 사람도 5분 안에 프로젝트의 큰 구조를 이해할 수 있도록 만든 빠른 안내서입니다.

이 문서만 먼저 읽고 나면 아래 질문에 답할 수 있어야 합니다.

- 이 프로젝트는 무엇을 하는가?
- 어디서 실행해야 하는가?
- 내가 수정해야 하는 파일은 무엇인가?
- 코드 흐름은 어떻게 되는가?
- 실행 후 무엇이 나오면 정상인가?

또 한 가지 중요한 전제는, 문서에 적힌 모든 경로는 예시이며 실제 실행 전에는 반드시 사용자 본인 환경 경로로 바꿔야 한다는 점입니다.

---

## 1. 이 프로젝트를 한 문장으로 설명하면

이 프로젝트는 **이미지 데이터를 읽어서 CNN 모델을 학습시키고, 그 모델이 이미지를 얼마나 잘 분류하는지 확인하는 실험 코드**입니다.

---

## 2. 전체 구조를 아주 짧게 보면

```text
데이터 폴더
   ↓
LoadData.py
   ↓
Experiments_Neuron.py
   ├─ initialize_model()   ← TrainModels.py
   ├─ get_data_loader()    ← LoadData.py
   ├─ train_model()        ← TrainModels.py
   └─ test_model()         ← TestModel.py
   ↓
콘솔 결과 + 그래프 출력
```

핵심은 아래 4개 파일입니다.

- `Experiments_Neuron.py`: 전체 실행을 지휘하는 메인 파일
- `LoadData.py`: 이미지 데이터를 읽는 파일
- `TrainModels.py`: 모델을 학습시키는 파일
- `TestModel.py`: 학습된 모델을 최종 평가하는 파일

---

## 2-1. 어디서 실행해야 하나요?

이 프로젝트는 **프로젝트 루트 폴더**에서 실행합니다.

```bash
cd /path/to/Neuron_AST_classification_code
python Experiments_Neuron.py
```

즉, 실행 위치와 메인 실행 파일은 아래처럼 기억하면 됩니다.

```text
실행 위치: /path/to/Neuron_AST_classification_code
실행 파일: Experiments_Neuron.py
```

---

## 3. 코드 흐름을 그림처럼 설명하면

### 단계 1. 실험 설정

`Experiments_Neuron.py`에서 아래를 정합니다.

- 어떤 데이터 폴더를 쓸지
- 어떤 모델을 쓸지
- 클래스 수가 몇 개인지
- 배치 크기
- epoch 수

```text
Experiments_Neuron.py
  └─ 실험 조건 설정
```

### 단계 2. 데이터 로딩

`LoadData.py`가 데이터 폴더 안의 `train / val / test` 이미지를 읽어옵니다.

```text
데이터 폴더
  └─ train
  └─ val
  └─ test
        ↓
LoadData.py
        ↓
DataLoader 생성
```

### 단계 3. 모델 생성

`TrainModels.py` 안의 `initialize_model()`이 ResNet 같은 CNN 모델을 준비합니다.

```text
Experiments_Neuron.py
   ↓
initialize_model()
   ↓
CNN 모델 준비
```

### 단계 4. 학습

`train_model()`이 여러 epoch 동안 `train`과 `val`을 반복하면서 학습합니다.

```text
train 데이터
   ↓
모델 학습
   ↓
val 데이터로 성능 확인
   ↓
epoch 반복
```

### 단계 5. 평가

`test_model()`이 `test` 데이터로 마지막 성능을 계산합니다.

```text
학습 완료 모델
   ↓
test 데이터 입력
   ↓
최종 Loss / Accuracy 출력
```

### 단계 6. 결과 확인

마지막에는 콘솔 숫자와 그래프를 봅니다.

```text
콘솔:
  train Loss / train Acc
  val Loss / val Acc
  test Loss / test Acc

그래프:
  정확도 곡선
  손실 곡선
```

---

## 4. 수정해야 하는 파일 / 수정하면 안 되는 파일

## 수정해도 되는 파일

처음 실험할 때 사용자가 실제로 열어볼 파일은 대부분 아래입니다.

### `Experiments_Neuron.py`

가장 중요합니다.  
아래 값을 수정할 가능성이 큽니다.

- `des_path`
- `args.model_name`
- `args.num_classes`
- `args.batch_size`
- `args.num_epochs`
- `args.feature_extract`

### `LoadData.py`

데이터 전처리 방식을 바꾸고 싶을 때만 수정합니다.

예:

- 이미지 크기
- grayscale 처리
- augmentation 방식

### `TrainModels.py`

모델 종류를 더 추가하거나 학습 로직을 바꾸고 싶을 때 봅니다.

초보자는 처음부터 자주 수정하지 않는 편이 좋습니다.

### `TestModel.py`

평가 방식을 바꾸고 싶을 때 봅니다.  
처음에는 읽기만 해도 충분합니다.

## 수정하면 안 되는 파일

초보자는 아래 파일/폴더를 건드리지 않는 것이 좋습니다.

### `.git/`

Git 내부 관리 폴더입니다.

### `.history/`

예전 코드 백업 파일 모음입니다.  
현재 실행 기준 파일이 아닙니다.

### `__pycache__/`

Python 실행 캐시입니다.

### `.vscode/`

편집기 설정 파일입니다.

### `LibPhySigProc/.git`

서브모듈 내부 Git 정보입니다.

### 이미지 파일 (`*.png`)

설명용 자료이므로 실행 로직 수정 대상이 아닙니다.

---

## 5. 각 파일의 역할 한 줄 요약

### 핵심 실행 파일

- `Experiments_Neuron.py`: 전체 실험을 시작하고 끝까지 연결하는 메인 파일
- `LoadData.py`: 이미지 폴더를 읽어 DataLoader를 만드는 파일
- `TrainModels.py`: 모델 생성과 학습 로직이 들어 있는 파일
- `TestModel.py`: 테스트 데이터로 최종 성능을 계산하는 파일

### 보조 파일

- `DiffPose12_Proc.py`: DiffPose12 데이터셋 전처리 연결 파일
- `GemStone_Proc.py`: 현재는 비어 있는 보조 파일
- `UMD_Proc.py`: 생체신호 데이터 전처리용 보조 파일

### 참고/설정 파일

- `README.md`: 저장소 시작 안내 문서
- `.gitignore`: Git에 올리지 않을 파일 목록
- `.gitmodules`: 서브모듈 연결 정보
- `workspace.code-workspace`: VS Code 작업공간 설정
- `submodule cmd.txt`: 서브모듈 관련 참고 명령

### 서브모듈 핵심 파일

- `LibPhySigProc/LibECGPPGBCG.py`: ECG/PPG/BCG 신호 처리 핵심 모듈
- `LibPhySigProc/LibGeneral.py`: 공통 유틸리티 모듈
- `LibPhySigProc/LibSigProc.py`: 저수준 신호 처리 유틸리티 모듈

---

## 6. `Experiments_Neuron.py` 기준 함수 호출 흐름

아래 순서대로 이해하면 됩니다.

```text
1. main 시작
2. 실험 설정값 지정
3. initialize_model() 호출
4. get_data_loader() 호출
5. train_model() 호출
6. test_model() 호출
7. 그래프 출력
```

좀 더 자세히 쓰면:

```text
Experiments_Neuron.py
  ├─ initialize_model(...)   -> TrainModels.py
  ├─ get_data_loader(...)    -> LoadData.py
  ├─ train_model(...)        -> TrainModels.py
  ├─ test_model(...)         -> TestModel.py
  └─ plt.show(...)           -> 결과 그래프 표시
```

즉, `Experiments_Neuron.py`는 직접 모든 일을 하지 않고, 다른 파일의 함수를 불러와 전체 실험을 조립하는 역할을 합니다.

---

## 7. 실행 후 정상 결과 체크리스트

실행 후 아래 항목이 보이면 대체로 정상입니다.

### 시작 직후

- `PyTorch Version:` 출력됨
- `Torchvision Version:` 출력됨
- 모델 구조가 출력됨
- `Initializing Datasets and Dataloaders...` 출력됨

### 데이터 로딩 단계

- train/test 이미지 샘플 창이 뜸
- 경로 오류가 나지 않음

### 학습 단계

- `Epoch 0/...` 형태 로그가 보임
- `train Loss`, `train Acc` 출력됨
- `val Loss`, `val Acc` 출력됨

### 평가 단계

- 마지막에 `test Loss`, `test Acc` 출력됨

### 종료 직전

- Accuracy 그래프가 뜸
- Loss 그래프가 뜸

---

## 8. 실행 후 이상한 상태 체크리스트

아래 중 하나면 점검이 필요합니다.

- 시작하자마자 경로 에러 발생
- `train/val/test` 폴더를 찾지 못함
- 클래스 수 오류 발생
- epoch 로그가 전혀 진행되지 않음
- `test Loss`, `test Acc`까지 가지 못함
- 그래프가 뜨지 않음

이 경우 가장 먼저 확인할 것은 아래입니다.

1. `des_path`가 맞는가
2. 데이터 구조가 `train/val/test/class` 형태인가
3. `args.num_classes`가 실제 클래스 수와 맞는가

---

## 9. 처음 보는 학생에게 추천하는 읽기 순서

1. 이 문서
2. `README.md`
3. `docs/04_run_step_by_step.md`
4. `Experiments_Neuron.py`
5. `LoadData.py`
6. `TrainModels.py`
7. `TestModel.py`

---

## 10. 마지막 한 줄 요약

이 프로젝트는 **`Experiments_Neuron.py`가 중심이 되어 데이터 로딩 → 모델 학습 → 테스트 평가 → 그래프 출력까지 이어지는 CNN 이미지 분류 실험 코드**입니다.
