# DETR의 Transformer vs NLP의 Transformer

## 1. 입력 표현 (Input Representation)
* NLP Transformer
  * 입력: 단어 시퀀스 (word embedding + positional encoding)
  * 각 토큰 = 문장의 한 단어
* DETR Transformer
  * 입력: CNN(ResNet등)에서 추출한 이미지 Feature Map
  * Feature Map을 펼쳐(flatten) -> 픽셀 위치별 벡터 시퀀스로 변환
  * 여기에 2D positional encoding을 더해서 순서(위치) 정보를 줌

* 차이: NLP는 1D 순서 정보, DETR은 2D 공간 위치 정보

## 2. Decoder 입력 (Object Query)
* NLP Transformer
  * Decoder 입력: 번역할 "타겟 문장"의 이전 단어들
  * 예: "나는 -> I", "학교에 -> to school"이런 식으로 순차적 예측
* DETR Transformer
  * Decoder 입력: Object Query라는 "학습 가능한 벡터" 세트
  * 각 Query = "하나의 객체 후보" 역할
  * Query들이 Encoder 출력 Feature와 Attention을 주고받으면서, "어떤 물체가 있는지, 어디 있는지"를 배우는 구조

* 차이: NLP는 "단어 예측", DETR은 "객체 슬롯(object slot) 예측"

## 3. 출력 (Output)
* NLP Transformer
  * Decoder 출력: 다음 단어의 확률 분포
  * 예: "나는" 다음에 올 단어 후보들 중 확률이 가장 큰 걸 선택
* DETR Transformer
  * Decoder 출력: 각 Object Query마다
    * 클래스 확률 (고양이, 사람, 자동차 등)
    * 바운딩 박스 좌표 (x, y, w, h)
  * 즉, Query = 탐지 박스 하나!

* 차이: NLP는 "텍스트 토큰", DETR은 "객체 + 위치"

## 4. 학습 방식
* NLP Transformer
  * Cross-Entropy로 단어 예측 오차 최소화
  * 순서가 있는 시퀀스 예측
* DETR Transformer
  * "헝가리안 매칭(Hungarian Matching)"을 이용해 예측 박스 ↔ 정답 박스를 최적으로 매칭
  * Loss = 클래스 손실 + 박스 손실(L1 + GIoU)

* 차이: NLP는 "다음 단어 맞히기", DETR은 "물체 매칭 + 박스 회귀"

## 5. 후처리(Post-Processing)
* NLP Transformer
  * Beam search 같은 디코딩 전략 필요
* DETR Transformer
  * NMS(Non-Max Suppression) 필요 없음
  * Query 출력이 이미 "중복 없는 객체 집합"이 되도록 학습
 

## 한 줄 요약
* NLP Transformer
  * 단어 시퀀스를 입력받아, 단어 시퀀스를 출력하는 모델
* DETR Transformer
  * 이미지 Feature를 입력받아, Object Query를 통해 바운딩 박스 + 클래스를 출력하는 모델

* 즉 구조는 비슷하지만 (Encoder-Decoder + Attention), 입력/출력/학습 목적이 달라서 DETR은 Transformer를 "시각적 객체 슬롯 예측기"로 변형한 것 이에요.

# DETR vs NLP Transformer 구조 비교

## 1.  Backbone (DETR만 있음)
* NLP Transformer
  * 따로 없음. 입력 = 단어 임베딩(Word Embedding)
  * 위치 정보는 1D Positional Encoding으로 추가
* DETR Transformer
  * CNN(ResNet-50/101 등)을 Backbone으로 사용
  * 이미지 -> Feature Map 추출 (예: 2048 x H/32 x W/ 32).
  * Feature Map을 flatten 해서 픽셀 단위 벡터 시퀀스로 변환
  * 여기에 2D Positional Encoding을 추가 (가로/세로 위치 정보)

* 차이: NLP는 단어 임베딩, DETR은 CNN Backbone Feature + 2D 위치 정보

## 2. Transformer Encoder
* NLP Transformer
  * 입력: 단어 임베딩 시퀀스
  * Self-Attention을 통해 문맥 정보 교환
  * 출력: 문맥이 반영된 단어 벡터 시퀀스
* DETR Transformer
  * 입력: Backbone Feature (flattened 2D grid)
  * Multi-Head Self-Attention으로 픽셀/패치 간 전역 관계 학습
  * 출력: 이미지의 전역 컨텍스트를 반영한 Feature 시퀀스

* 같은 점: Self-Attention 구조 동일
* 다른 점: NLP는 단어 간 관계, DETR은 이미지 위치 간 관계

## 3. Transformer Decoder
* NLP Transformer
  * 입력: 번역할 문장의 이전 단어들(shifted right)
  * Masked Self-Attention -> 이전 단어까지만 참조
  * Cross-Attention으로 Encoder 출력(원문)과 상호작용
  * 출력: 다음 단어 분포
* DETR Transformer
  * 입력: Object Query (학습 가능한 고정 벡터들, 예: 100개)
  * Self-Attention -> Object Query 간 상호작용
  * Cross-Attention -> Encoder 출력 Feature와 상호작용
  * 출력: 각 Query가 하나의 "객체 후보" 벡터

* 차이: NLP는 "다음 단어 예측", DETR은 "객체 슬롯 예측"

## 4. Feed-Forward Networks (FFN, Prediction Head)
* NLP Transformer
  * Decoder 출력 -> Linear Layer + Softmax -> 다음 단어 확률
* DETR Transformer
  * Decoder 출력(Object Query) -> FFN 두 개로 분리
    1. 클래스 예측 Head: Linear -> Softmax(클래스 + 배경)
    2. 박스 예측 Head: MLP (3-layer) -> (x, y, w, h)좌표 (normalized)

* 차이: NLP는 단어 확률 1개만, DETR은 "클래스 + 박스 좌표" 두 가지 출력

## 전체 흐름 요약 (DETR)
1. Backbone (ResNet)
   * 이미지 입력 -> Feature Map 추출 -> Flatten + Positional Encoding
2. Encoder (Self-Attention)
   * Feature들끼리 전역적 관계 학습
3. Decoder (Object Query + Cross-Attention)
   * Object Query들이 Feature와 상호작용 -> "이 Query가 담당할 객체 정보" 학습
4. FFN (Prediction Head)
   * 각 Query -> (클래스 확률 + 바운딩 박스 좌표)
5. Hungarian Matching
   * 출력된 고정 개수의 박스 ↔ 실제 GT 박스 매칭
6. Loss 계산 (Classification + Box Loss)

## 한 줄 요약
* NLP Transformer: 단어 시퀀스를 입력받아, 다음 단어를 출력하는 구조
* DETR Transformer: CNN Backbone Feature를 입력받아, Object Query를 통해 클래스 + 바운딩 박스를 출력하는 구조
* 즉, 구조는 동일하지만 입력/출력/FFN Head가 다르고, Backbone이 추가된 게 DETR의 가장 큰 차이에






















































