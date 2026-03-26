# File Guide

이 문서는 `Neuron_AST_classification_code` 안의 파일들을 처음 받은 사람 관점에서 설명하기 위한 문서입니다.

핵심 원칙은 다음과 같습니다.

- 실제로 읽고 실행할 가능성이 높은 파일은 자세히 설명합니다.
- 기록성 파일이나 시스템 파일은 “무엇인지”와 “건드려야 하는지”만 설명합니다.
- `.git`, `.history`, `__pycache__`처럼 매우 많은 파일이 있는 경우는 파일 하나씩이 아니라 묶어서 설명합니다.

## A. 가장 중요한 핵심 실행 파일

### `Experiments_Neuron.py`

이 저장소의 메인 실행 파일입니다.

역할:

- 실험 설정값 지정
- 모델 선택
- 데이터 로딩 호출
- 학습 호출
- 테스트 호출
- 결과 그래프 출력

처음 받은 사람이 가장 먼저 열어봐야 하는 파일입니다.

### `LoadData.py`

이미지 데이터 폴더를 읽어서 학습에 사용할 DataLoader를 만드는 파일입니다.

역할:

- 이미지 폴더 구조 읽기
- transform 적용
- grayscale/resize/normalize 처리
- `(image, label, path)` 형태 반환

### `TrainModels.py`

모델 학습의 핵심 로직이 들어 있는 파일입니다.

역할:

- epoch 반복
- train/val 분리 처리
- loss 계산
- accuracy 계산
- best model weight 저장
- 모델 초기화 함수 제공

### `TestModel.py`

최종 테스트 데이터셋으로 성능을 확인하는 파일입니다.

역할:

- test 단계 loss 계산
- test accuracy 계산
- 최종 수치 출력

## B. 루트의 보조 스크립트 파일

### `DiffPose12_Proc.py`

DiffPose12 데이터셋용 전처리 연결 파일입니다.

역할:

- interval 이름 정의
- `UMD_Proc.py`의 공통 전처리 함수와 연결

### `GemStone_Proc.py`

현재 파일 내용은 비어 있습니다.

의미:

- GemStone 관련 처리를 분리하려고 만들어졌을 가능성이 있지만, 현재 저장소 기준으로는 실사용 로직이 들어 있지 않습니다.

### `UMD_Proc.py`

UMD/GemStone 계열 생체신호 데이터를 전처리하는 함수들이 모여 있는 파일입니다.

역할:

- raw biosignal 데이터 구조 정리
- 채널별 신호 분리
- 필터링
- beat 단위 처리 보조

이 파일은 이미지 분류 메인 흐름보다 생체신호 전처리 쪽 성격이 강합니다.

## C. 루트의 문서/설정/기타 파일

### `README.md`

저장소 시작 문서입니다.  
어디서부터 읽어야 하는지 안내하는 허브 역할을 합니다.

### `LICENSE`

저장소 라이선스 정보입니다.

### `.gitignore`

Git에 올리지 않을 파일/폴더 목록을 적어 둔 파일입니다.

예:

- `__pycache__`
- `DataSets/*`
- `.history`
- `log`

### `.gitmodules`

서브모듈 설정 파일입니다.  
이 저장소가 `LibPhySigProc`를 별도 Git 저장소처럼 연결하고 있음을 보여줍니다.

### `.gitattributes`

Git 속성 설정 파일입니다.

### `submodule cmd.txt`

서브모듈 관련 명령을 적어 둔 참고용 텍스트 파일로 보입니다.

### `workspace.code-workspace`

VS Code 작업공간 설정 파일입니다.

처음 받은 사람이 꼭 수정해야 하는 파일은 아닙니다.

## D. 이미지 자료 파일

### `Optics Datasets.png`

옵틱스 데이터셋 관련 참고 이미지로 보입니다.

### `Stain Datasets.png`

염색 데이터셋 관련 참고 이미지입니다.

### `Stain Datasets_500.png`

염색 데이터셋 관련 참고 이미지의 다른 버전으로 보입니다.

이 파일들은 실행용 코드가 아니라 설명/정리용 자료입니다.

## E. `LibPhySigProc` 폴더

`LibPhySigProc`는 생체신호 처리용 보조 라이브러리 성격의 폴더입니다.

초보자 입장에서는 “메인 분류 실험을 도와주는 별도 유틸리티 묶음” 정도로 이해하면 됩니다.

### `LibPhySigProc/README.md`

서브모듈 자체 소개 문서입니다.

### `LibPhySigProc/LibECGPPGBCG.py`

ECG, PPG, BCG 관련 신호 처리 기능이 많이 모여 있는 핵심 파일입니다.

역할:

- 데이터셋 로드
- 신호 필터링
- beat 단위 처리
- 공통 신호 처리 클래스 제공

### `LibPhySigProc/LibGeneral.py`

일반 유틸리티 모음 파일입니다.

역할:

- 경로/폴더 유틸
- 로깅 클래스
- 공통적인 도움 함수

### `LibPhySigProc/LibSigProc.py`

신호 처리 계산용 저수준 유틸리티 파일입니다.

역할:

- bandpass/highpass/lowpass 필터 생성
- peak 탐색
- trend 계산
- 길이가 다른 배열 평균 계산

### `LibPhySigProc/Test.ipynb`

노트북 기반 테스트/실험 파일입니다.

### `LibPhySigProc/LICENSE`

서브모듈 라이선스 파일입니다.

### `LibPhySigProc/.git`

서브모듈의 Git 내부 정보입니다. 수정 대상이 아닙니다.

### `LibPhySigProc/__pycache__/`

Python 실행 캐시입니다. 수정 대상이 아닙니다.

## F. 개발 기록 및 시스템 폴더

### `.history/`

이 폴더는 과거 버전 백업 파일 모음입니다.

특징:

- 날짜가 붙은 이전 버전 파일들이 매우 많습니다.
- 실제 현재 실행 기준 파일은 아닙니다.
- 처음 받은 사람은 보통 이 폴더를 건드릴 필요가 없습니다.

### `.vscode/`

VS Code 편집기 설정 폴더입니다.

포함 파일:

- `launch.json`
- `settings.json`

### `__pycache__/`

Python이 실행되면서 자동 생성한 캐시 파일 폴더입니다.  
직접 수정할 필요가 없습니다.

### `.git/`

Git 저장소 내부 관리 폴더입니다.  
프로젝트 코드 설명 대상은 아니고, 버전 관리 시스템이 사용하는 내부 폴더입니다.

## G. 처음 받은 사람이 실제로 우선순위를 두고 봐야 하는 파일

처음 보는 사람에게 추천하는 우선순위는 아래와 같습니다.

1. `README.md`
2. `docs/00_start_here.md`
3. `Experiments_Neuron.py`
4. `LoadData.py`
5. `TrainModels.py`
6. `TestModel.py`
7. 필요 시 `LibPhySigProc` 핵심 파일

그 외 파일들은 “필요할 때 찾아보는 참고 자료”로 생각하면 됩니다.
