# Dataset Structure

이 프로젝트는 이미지 분류 프로젝트이므로, 데이터 폴더 구조가 매우 중요합니다.

## 1. 코드가 기대하는 데이터 형태

`LoadData.py`는 `torchvision.datasets.ImageFolder` 방식을 사용합니다.  
이 방식은 폴더 이름을 클래스 이름으로 해석합니다.

즉, 데이터는 아래와 같은 구조여야 합니다.

```text
데이터 루트/
  train/
    class_0/
      image1.png
      image2.png
    class_1/
      image3.png
      image4.png
  val/
    class_0/
    class_1/
  test/
    class_0/
    class_1/
```

## 2. 각 폴더 의미

- `train`: 모델이 배우는 데이터
- `val`: 학습 중 성능 점검 데이터
- `test`: 마지막 최종 평가 데이터

## 3. 클래스 폴더는 왜 필요한가요?

코드는 클래스 이름을 따로 파일에서 읽지 않고, 폴더 이름으로 구분합니다.

예를 들어:

- `train/normal/`
- `train/abnormal/`

처럼 폴더가 나뉘어 있으면, 모델은 폴더 기준으로 이미지를 분류 대상에 맞게 읽습니다.

## 4. 현재 코드와 연결되는 부분

메인 파일 [Experiments_Neuron.py](../Experiments_Neuron.py)에는 데이터 루트 경로가 `des_path`로 들어 있습니다.

예:

```python
des_path = "/path/to/your_dataset_root"
```

이 경로 아래에 `train`, `val`, `test`가 있어야 합니다.

즉, `des_path`는 “데이터 루트 폴더”입니다.

## 5. 이미지 전처리에서 일어나는 일

`LoadData.py`에서 이미지에 아래 처리가 적용됩니다.

- 크기 조정: `224 x 224`
- grayscale 변환 후 3채널로 맞춤
- contrast 조정
- tensor 변환
- normalization

이 말은, 원본 이미지가 조금 다르더라도 모델에 들어가기 전에는 일정한 형태로 바뀐다는 뜻입니다.

## 6. 데이터 준비 시 꼭 확인할 것

- `train`, `val`, `test`가 모두 있는가
- 각 폴더 안에 클래스 폴더가 있는가
- 클래스 폴더 안에 실제 이미지 파일이 있는가
- 클래스 수가 `args.num_classes`와 맞는가

현재 메인 파일에서는 다음처럼 클래스 수를 2로 두고 있습니다.

```python
args.num_classes = 2
```

즉, 기본 가정은 2-class 분류입니다.

## 7. 잘못된 데이터 구조 예시

아래 구조는 문제가 될 수 있습니다.

```text
데이터 루트/
  train/
    image1.png
    image2.png
```

이 경우는 클래스 폴더가 없어서 `ImageFolder`가 정상적으로 동작하지 않을 수 있습니다.

## 8. 데이터 구조가 맞는지 빠르게 확인하는 법

아래를 스스로 확인하세요.

- 데이터 루트 안에 `train/val/test`가 있는가
- 각 폴더 안에 최소 2개의 클래스 폴더가 있는가
- 이미지가 실제로 열리는 파일인가

이 구조가 맞아야 `docs/04_run_step_by_step.md`대로 실행했을 때 정상적으로 DataLoader가 만들어집니다.
