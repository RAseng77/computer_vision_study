# Segment Anything

## 1.Task (목표 과제)
* 기존 세그멘테이션 모델은 특정 데이터셋/도메인에 맞춰 학습해야 했음
* Segment Anything의 Task는 "모든 세그맨테이션(All-purpose segmentation)"
  * 어떤 이미지든, 어떤 객체든, 라벨이 없어도 세그맨테이션을 수행
* 사용자 입력(프롬프트)을 기반으로 다양한 세그맨테이션 수행 가능:
  * Point prompt: 한 점 찍으면 그 물체 마스크 출력
  * Box prompt: 사각형 그리면 해당 영역 세그맨테이션
  * Text prompt: (확장 연구에서) "고양이"라고 하면 고양이 세그맨테이션

* -> 즉, 범용 세그맨테이션 프레임워크로서의 역할 Segment Anything

## 1.Task (목표 과제)
* 기존 세그멘테이션 모델은 특정 데이터셋/도메인에 맞춰 학습해야 했음
* Segment Anything의 Task는 "모든 세그맨테이션(All-purpose segmentation)"
  * 어떤 이미지든, 어떤 객체든, 라벨이 없어도 세그맨테이션을 수행
* 사용자 입력(프롬프트)을 기반으로 다양한 세그맨테이션 수행 가능:
  * Point prompt: 한 점 찍으면 그 물체 마스크 출력
  * Box prompt: 사각형 그리면 해당 영역 세그맨테이션
  * Text prompt: (확장 연구에서) "고양이"라고 하면 고양이 세그맨테이션

* -> 즉, 범용 세그맨테이션 프레임워크로서의 역할

## 2. Pre-training (사전 학습)
* SAM은 11억 개의 mask를 학습에 사용. (기존 세그맨테이션 연구보다 수천 배 큼)
* 사전 학습 목표:
  * 입력 이미지 + 프롬프트 -> 정밀한 마스크 출력
  * 다양한 도메인(일상, 의료, 위성, 산업 등)을 포함해 범용성 확보
* Vision Transformer (ViT-Huge, 632M 파라미터)를 Backbone으로 사용
* 대규모 데이터셋으로 Generalizable Feature Representation을 학습

## 3.Zero-shot Transfer
* SAM의 핵심 장점 중 하나
* 새로운 데이터셋에 대해 추가 학습 없이도(Zero-shot) 높은 성능을 발휘
* 예시:
  * 의료 영상에 fine-tuning 안 했는데도, 세포나 장기를 분할 가능
  * 위성사진, 드론 영상에서도 잘 동작
* 이유: 거대한 데이터와 범용 구조 덕분에 generalization이 강력함

## 4.Model Architecture
* SAM은 크게 3가지 모듈로 구성됨:
1. Image Encoder (ViT-Huge)
   * 이미지를 입력받아 고차원 특징 맵 생성
2. Prompt Encoder
   * 사용자 입력(Point, Box, Text 등)을 벡터로 변환
   * 이미지 특징과 결합할 수 있도록 설계
3. Mask Decoder
   * Image + Prompt embedding을 결합 -> 마스크 예측
   * 경량 Transformer 기반 구조 -> 빠른 마스크 출력
   * 한 번에 여러 후보 마스크 + 점수 출력 (Ambiguity 대응)
  
## 5. Data Engine (데이터 엔진)
* SAM의 또 다른 혁신 포인트
* 기존 세그맨테이션 연구는 **라벨링 비용(픽셀 단위 마스크)** 이 너무 높았음
* SAM 팀은 **데이터 엔진(Data Engine)** 을 개발:
  1. 초기 모델이 rough mask 생성
  2. 사람이 빠르게 수정 (point 클릭)
  3. 모델이 점점 더 좋아짐 -> 라벨링 속도 빨라짐
* 이 과정을 반복해서 11억 개 마스크를 만든 것
* 즉, 모델 + 사람 in the loop 방식으로 대규모 데이터셋 구츅

## 6. Segment Anything에서 꼭 기억해야 할 것
1. Task: 범용 세그맨테이션 (Anything Segmentation)
2. Pre-training: 11억 마스크, 세계 최대 규모 세그멘테이션 학습
3. Zero-shot Transfer: 새로운 도메인에도 잘 작동 (추가 학습 불필요)
4. Model Architecture: Image Encoder (ViT) + Prompt Encoder + Mask Decoder
5. Data Engine: 모델-사람 협업으로 초대규모 데이터 구축
6. 키워드: Promptable segmentation (점, 박스, 텍스트 입력으로 조작)
7. 실용성: 연구 + 산업에서 이미지/비디오 분할의 새로운 표준 도구가 됨

## 한 줄 요약
* Segment Anything (SAM)은
* "이미지 세그멘테이션의 GPT 같은 범용 모델"로,
* 프롬프트 기반 + 대규모 사전학습 + Zero-shot 전이를 특징으로 하며,
* 11억 개 마스크를 만든 Data Engine 덕분에 지금까지 나온 세그맨테이션 모델 중 가장 범용적입니다.

-------------------------------------------------------------------------------------------------------------------

## 1. Image Encoder
* SAM의 backbone은 Vision Transformer (ViT-Huge)
* 역할: 입력 이미지를 고해상도 Feature Map으로 변환
* 특징:
  * 이미지 전체를 패치 단위(16x16)로 쪼개어 Transformer에 넣음
  * 전역 Attention -> 물체의 크기/위치와 무관하게 강한 표현력
* 출력: 압축된 feature embedding (2D grid 형태)
* 즉, 이미지를 이해하는 뇌 역할

## 2.Prompt Encoder
SAM의 가장 큰 차별점 = "사용자가 어떤 객체를 보고 싶은지" 알려줄 수 있음
* 입력 프롬프트 종류
  * Point (좌표 한 개)
  * Box (사각형)
  * Mask (부분 마스크)
  * (추가 연구: Text, 음성 등)
* 처리 방법
  * 좌표나 박스를 positional embedding으로 변환
  * 기존 이미지 feature 공간과 같은 차원의 embedding으로 매핑
* 즉, 사용자의 의도를 embedding으로 바꿔서 모델에게 전달

## 3. Mask Decoder
* Image Encoder의 feature + Prompt Encoder의 embedding -> 입력
* 작은 Transformer block으로 설계 (빠른 추론을 위해 경량화)
* 출력:
  * 픽셀 단위 binary mask
  * 여러 개의 **후보 마스크(candidate mask)** 와 각각의 confidence score.
* 즉, **이 영역일 가능성이 있다** 라는 마스크들을 동시에 출력

## 4. 모호성(Ambiguity) 문제 해결
* 기존 세그멘테이션 모델은 모호할 때(예: 점을 자동차 바퀴에 찍었는데, 바퀴만 할지? 자동차 전체를 할지?) -> 하나의 마스크만 내놔서 실패할 때가 많음
SAM의 전략:
  * Mask Decoder가 여러 후보 마스크를 동시에 생성
  * 각 마스크는 confidence score(신뢰도 점수)를 가짐
  * 사용자는 그중 하나를 선택하거나, 추가 prompt(점/박스)를 줘서 refinement 가능
* 즉, 애매할 땐 모델이 "후보 세트"를 주고, 사용자가 고르게 한다 -> 모호성 해소

## 한 줄 요약
* Image Encoder: ViT-Huge로 이미지 전체 feature 추출
* Prompt Encoder: 사용자 입력(점, 박스, 마스크)을 embedding으로 변환
* Mask Decoder: 이미지 + 프롬프트를 받아 후보 마스크 + 점수 출력
* 모호성 해결: 애매할 때는 여러 마스크 후보를 내놓고, 사용자가 refinement 가능


















 




















































































































































































