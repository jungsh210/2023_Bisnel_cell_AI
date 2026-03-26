# Start Here

이 문서는 `Neuron_AST_classification_code` 코드 인수 인수인계 및 학습을 위한 문서입니다.

## 가장 먼저 답해야 하는 4가지 질문

### 1. 이 프로젝트는 무엇을 하는가?

이 프로젝트는 뉴런 이미지 데이터를 읽어서 CNN 모델을 학습시키고, 그 모델이 이미지를 얼마나 잘 분류하는지 확인하는 이미지 분류 실험 코드입니다.

### 2. 어디서 실행해야 하는가?

프로젝트 루트 폴더에서 실행합니다.  
메인 실행 파일은 `Experiments_Neuron.py` 입니다.

### 3. 어떤 파일을 수정해야 하는가?

처음 실행할 때 가장 먼저 확인해야 하는 파일은 `Experiments_Neuron.py` 입니다.

주로 수정하는 값:

- `des_path`
- `args.model_name`
- `args.num_classes`
- `args.batch_size`
- `args.num_epochs`
- `args.feature_extract`

### 4. 코드 흐름은 어떻게 되는가?

가장 짧게 쓰면 아래 흐름입니다.

```text
Experiments_Neuron.py
  -> initialize_model()  in TrainModels.py
  -> get_data_loader()   in LoadData.py
  -> train_model()       in TrainModels.py
  -> test_model()        in TestModel.py
  -> 그래프 출력
```

빠르게 구조만 보고 싶다면 [docs/08_quick_structure_guide.md](08_quick_structure_guide.md)를 먼저 읽으면 됩니다.

## 이 프로젝트는 무엇을 하나요?

이 프로젝트는 뉴런 이미지 데이터를 분류하는 딥러닝 모델을 학습하고, 학습된 모델이 새 이미지도 잘 분류하는지 확인하는 실험용 코드입니다.

쉽게 말하면 다음과 같습니다.

1. 준비된 이미지 데이터를 `train`, `val`, `test`로 나눕니다.
2. 모델이 `train` 데이터를 보면서 학습합니다.
3. 학습 중간중간 `val` 데이터로 성능을 확인합니다.
4. 마지막에는 `test` 데이터로 최종 성능을 확인합니다.
5. 출력되는 정확도와 손실값을 보고 모델이 잘 학습되었는지 판단합니다.

## 이 저장소를 볼 때 무엇을 먼저 알아야 하나요?

- 메인 실행 파일은 `Experiments_Neuron.py`입니다.
- 실행 위치는 프로젝트 루트 폴더입니다.
- 문서에 나오는 모든 경로는 예시이므로, 사용자 본인 환경 경로에 맞게 바꿔야 합니다.
- 데이터는 이미지 폴더 형태여야 합니다.
- 실행 전에는 데이터 경로를 직접 수정해야 합니다.
- 실행 결과는 주로 콘솔 출력과 그래프 창으로 확인합니다.
- Python, PyTorch를 잘 몰라도 문서 순서대로 보면 실행 흐름을 이해할 수 있게 정리해 두었습니다.

## 추천 학습 순서

1. 환경 세팅 영상 보기
2. `docs/08_quick_structure_guide.md` 읽기
3. `docs/01_project_big_picture.md` 읽기
4. `docs/02_environment_and_prerequisites.md` 읽기
5. `docs/03_dataset_structure.md` 읽기
6. `docs/04_run_step_by_step.md`를 보며 실제 실행 준비
7. `docs/05_expected_results.md`를 보며 결과 확인
8. 코드가 궁금하면 `docs/06_file_guide.md` 참고

## 이 프로젝트에서 최종적으로 알고 싶은 것은 무엇인가요?

핵심은 “이 모델이 주어진 뉴런 이미지 분류 문제를 어느 정도 잘 해결하는가?”입니다.

실행 후에는 다음을 확인해야 합니다.

- 학습이 정상적으로 진행되었는가
- 검증 정확도가 어느 정도까지 올라가는가
- 테스트 정확도가 충분히 좋은가
- 그래프가 이상하게 흔들리거나 과적합처럼 보이지는 않는가

이 결과를 바탕으로 다음 실험을 할 수 있습니다.

- 다른 모델 사용
- epoch 수 변경
- 배치 크기 변경
- 데이터셋 변경
- 전처리/증강 방법 변경

## 처음부터 끝까지 딱 한 줄로 요약하면

이 저장소는 “뉴런 이미지 분류 실험을 실행하고, 정확도와 손실값을 보면서 모델 성능을 확인하는 프로젝트”입니다.
