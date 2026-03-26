# Environment And Prerequisites

이 문서는 환경 세팅 영상을 글로 보완하는 문서입니다.

## 1. 먼저 알아둘 것

이 프로젝트는 Python으로 작성되어 있고, 딥러닝 학습에 PyTorch를 사용합니다.

처음 듣는 용어라면 아래처럼 이해하면 충분합니다.

- Python: 코드를 실행하는 프로그래밍 언어
- PyTorch: 딥러닝 모델을 만들고 학습시키는 라이브러리
- torchvision: 이미지 데이터와 이미지 모델을 다루는 PyTorch 보조 라이브러리

## 2. 필요한 기본 환경

현재 코드에서 사용되는 주요 라이브러리는 아래와 같습니다.

- Python
- torch
- torchvision
- numpy
- matplotlib
- pandas
- torchinfo
- PIL(Pillow)
- scipy
- neurokit2

실제 설치는 환경 세팅 영상을 우선 기준으로 삼고, 문서는 “무엇이 필요한지”를 확인하는 용도로 사용하면 됩니다.

## 3. 실행 전에 꼭 확인할 것

아래 체크리스트를 먼저 확인하세요.

- Python이 실행되는가
- 필요한 라이브러리가 설치되어 있는가
- 프로젝트 폴더가 정상적으로 열리는가
- 데이터 폴더가 준비되어 있는가
- `Experiments_Neuron.py`의 `des_path`가 내 컴퓨터 경로와 맞는가

## 4. 가장 중요한 경로 설정

현재 메인 파일에는 아래와 같은 데이터 경로가 직접 적혀 있습니다.

```python
des_path = "/path/to/your_dataset_root"
```

이 값은 작성자의 컴퓨터 기준 경로입니다.  
따라서 다른 컴퓨터에서는 그대로 실행하면 거의 반드시 경로 오류가 발생합니다.

즉, 실행 전에 반드시 본인 데이터 폴더 위치로 수정해야 합니다.

## 5. GPU가 꼭 필요한가요?

꼭 그렇지는 않습니다.

코드는 아래처럼 GPU가 있으면 GPU를 쓰고, 없으면 CPU를 쓰도록 되어 있습니다.

```python
device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
```

의미:

- GPU 있음: 더 빠르게 학습 가능
- GPU 없음: CPU로도 실행은 가능하지만 느릴 수 있음

## 6. 정상 설치 확인용 간단 점검

터미널 또는 명령 프롬프트에서 아래를 확인합니다.

```bash
python --version
```

그리고 Python이 열리면 아래처럼 라이브러리 import가 되는지 확인합니다.

```python
import torch
import torchvision
import numpy
import matplotlib
```

오류가 없으면 기본 환경은 준비된 것입니다.

## 7. 자주 막히는 문제

### 1) `ModuleNotFoundError`

의미:

- 필요한 라이브러리가 설치되지 않았다는 뜻입니다.

확인:

- 환경 세팅 영상대로 설치했는지
- 올바른 가상환경 또는 Python 환경을 사용 중인지

### 2) 데이터 경로 오류

의미:

- `des_path`가 실제 데이터 위치와 다르다는 뜻입니다.

확인:

- 경로 오타
- 폴더 이름 차이
- `train`, `val`, `test` 폴더 존재 여부

### 3) 이미지 로딩 오류

의미:

- 데이터 폴더 구조가 코드가 기대한 형태와 다를 수 있습니다.

확인:

- 클래스별 폴더가 있는지
- 이미지 파일이 실제로 들어 있는지

### 4) 실행은 되는데 매우 느림

의미:

- CPU로 실행 중일 가능성이 큽니다.

확인:

- GPU가 잡혔는지
- batch size가 너무 크거나 작은지

## 8. 실행 전 마지막 체크리스트

- 환경 세팅 영상 확인 완료
- Python 실행 확인
- 라이브러리 import 확인
- 데이터 폴더 준비 완료
- `des_path` 수정 완료
- `train/val/test` 구조 확인 완료

이 체크리스트가 끝났다면 다음 문서인 `docs/03_dataset_structure.md`와 `docs/04_run_step_by_step.md`를 보면 됩니다.
