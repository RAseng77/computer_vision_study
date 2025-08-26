# EfficientDet 핵심 아이디어
EfficientDet은 Google이 발표한 작고 빠르면서도 정확한 객체 탐지기입니다. 기존의 탐지 모델들은 크면 정확하지만 느리고,
작으면 빠르지만 정확도가 떨어졌는데...
> EfficientDet은 효율적인 설계(EfficientNet 백본 + BiFPN + Compound Scalling)로 작고 빠르면서도 높은 정확도를 동시에 달성했습니다.

## EfficientDet 주요 특징
1. EfficientNet Backbone
  * 분류 모델 EfficientNet을 탐지 백본으로 사용
  * MBConv(Depthwise Separable Conv), Swish 활성화, Squeeze-and-Excitation(SE) 사용
  * -> 적은 연산량으로도 강력한 특징 추출
2. BiFPN(Bi-directional Feature Pyramid Network)
  * 기존 FPN(위 -> 아래), PANet(아래 -> 위) 구조를 합친 양방향 구조
  * 특징 맵을 가중치 합산(weighted sum)으로 융합 -> 중요한 특징에 더 집중
  * -> 작은 물체/큰 물체 모두 잘 잡음
3. Class/Box prediction Head (Shared & Lightweight)
  * 여러 스케일에서 같은 가벼운 예측기를 공유해서 파라미터 수 절약
  * Depthwise Conv 기반 -> 연산량↓
4. Compound Scalling (통합 스케일링 법칙)
  * EfficientNet 처럼
    * 해상도 (Resolution)
    * 네트워크 깊이 (Depth)
    * 네트워크 너비 (Width)
  * 를 동시에 조절해서 모델 크기를 균형 있게 키움
  * EfficientDet-D0 ~ D7까지 다양한 크기의 모델 제공
  * -> 작은 모델(D0)은 모바일에서도 동작, 큰 모델(D7)은 SOTA 성능 달성.

## EfficientDet 동작 방식
1. 이미지를 EfficientNet Backbone으로 특징 추출
2. BiFPN에서 여러 레벨 특징 맵을 양방향으로 합치고 강조
3. Prediction Head에서 각 스케일별로
  * 바운딩 박스 좌표 (x,y,w,h)
  * 클래스 확률
  * 예측
4. NMS 후 최종 결과 출력

## 한 줄 요약
EfficientDet은 EfficientNet + BiFPN + Compound Scalling 으로 "작고 빠르면서 정확한" 균형 잡힌 객체 탐지 모델입니다.

## 왜 Weighted Feature Fusion이 필요한가?
기존 FPN(Pyramid)이나 PANet에서는 서로 다른 해상도의 Feature Map을 그냥 덧셈 또는 평균으로 합쳤습니다. 하지만 이렇게 하면 각 Feature Map이 동등한 비중으로 섞이기 때문에, 실제로 중요한 Feature(예: 작은 물체의 고해상도 특징 vs 큰 물체의 저해상도 특징)를 잘 살리지 못하는 경우가 생겼습니다.

## Weighted Feature Fusion 아이디어
EfficientDet에서는 단순 합산 대신 학습 가능한 가중치를 줍니다.
  * 각 입력 Feature Map xi 마다 ""양의 가중치 wi >= 0"" 를 부여
  * 융합할 때 단순 합이 아니라 가중 평균으로 계산:
  * $$
\text{Output} = \frac{\sum_{i=1}^{n} w_i \cdot x_i}{\sum_{i=1}^{n} w_i + \epsilon}
$$
  * 여기서 𝜖은 0으로 나누는 걸 방지하기 위한 작은 값
  * wi는 학습 중 자동으로 업데이트됨 -> 어떤 Feature가 중요한지 모델이 스스로 배움

## Weighted Feature Fusion 장점
1. 적응적 중요도 학습
   * 데이터와 상황에 따라 "어떤 스케일의 Feature가 더 중요한지"를 네트워크가 학습
   * 예: 작은 물체가 많은 데이터 -> 고해상도 Feature의 가중치↑.
2. 성능 향상
   * 단순 sum보다 mAP가 개선됨
3. 계산 효율성 유지
   * 별도의 큰 연산이 아니라 가중치 파라미터 몇 개만 추가되기 때문에 가볍다.
  
## 한 줄 요약
EfficientDet의 Weighted Feature Fusion은 서로 다른 해상도의 Feature Map을 단순히 더하지 않고, 학습 가능한 가중치로 합쳐서 중요한 정보를 더 잘 살리는 기법입니다.


































