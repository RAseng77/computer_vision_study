# Rotated Object Detection (회전 객체 탐지)
## 1.개념
* 일반적인 객체 탐지(Object Detection)는 수평(Horizontal) 박스(HBB, Horizontal Bounding Box)를 사용합니다.
* 하지만 항공/위성 이미지, OCR(문자 인식), 도로 표지판, 드론 영상 같은 경우
* 물체가 회전된 상태로 많이 등장 -> 수평 박스로는 물체를 정확히 감싸지 못함.
* 그래서 Rotated Bounding Box (OBB, Oriented Bounding Box)를 사용하는 탐지 방법이 연구됨
  * OBB는 중심 좌표(x,y), 폭 w, 높이 h, 각도 θ 로 정의
  * -> 회전된 객체도 정확히 감쌀 수 있음
 
## 2.문제점
* 수평 박스에서 각도를 추가하면 **회전 각도 회귀 문제** 발생 -> 불연속성 문제(예: -90°와 +90°)
* 작은 물체/밀집된 물체에서 예측 안정성이 떨어짐.
* 그래서 여러 연구들이 이 문제를 풀려고 다양한 방법을 제안했습니다.

-----------------------------------------------------------------------------------------------------------------------------------

# Gliding Vertex on the Horizontal Bounding Box (2020)
## 개념
* 기본 아이디어: 회전된 박스를 직접 회귀하지 않고, 수평 박스(HBB)에서 꼭짓점을 **"미끄러뜨려(glide)"** 위치를 보정해서 OBB를 얻자.

## 방식
1. 먼저 일반 Detector로 수평 박스(HBB) 예측
2. 그 박스의 네 꼭짓점(vertex)을 offset(Δx, Δy) 만큼 이동시켜서
   * 물체를 감싸는 회전 박스(OBB) 생성
3. 즉, "HBB + 꼭짓점 이동" 방식으로 간접적으로 회전 박스를 얻

## 장점
* 직접 각도를 회귀하는 방식보다 안정적
* 다양한 각도의 물체도 잘 탐지
* OBB 데이터셋(예: DOTA)에서 좋은 성능

# RBox-CNN (2018)
## 개념
* 기존 Faster R-CNN을 Rotated Box(RBox) 예측 가능하게 확장한 모델
* RBox = (x,y,w,h,θ)

## 방식
1. Region Proposal Network (RPN) 단계에서부터
   * Anchor를 수평이 아닌, 다양한 각도의 Rotated Anchor로 설계
2. Classification + Regression Head 에서
   * 클래스 예측 + (x, y, w, h, θ) 회귀

## 특징
* OBB를 직접 회귀하므로 -> 각도 정보까지 정확히 예측 가능
* 수평 박스 기반 방법보다 더 자연스럽게 회전 객체 탐지

## 단점
* 각도 불연속성 문제(예: -90° vs +90°) 존재
* Anchor 설계가 복잡해짐

-----------------------------------------------------------------------------------------------------------------------------------

# Gliding Vertex on the Horizontal Bounding Box (2020)

## 1. Faster R-CNN with FPN
* Gliding vertex 는 **기본 Detector로 Faster R-CNN + FPN(Feature Pyramid Network)** 을 사용합니다.
* 이유:
  * FPN은 다중 스케일 특징을 활용해 작은 물체까지 잘 탐지할 수 있음
  * 회전 객체 데이터셋(DOTA 등)은 작은 비행기, 배, 차량 등 다양한 크기의 물체가 많기 때문에 FPN이 필수.
* 흐름:
  * Backbone(CNN) => FPN으로 다중 스케일 Feature -> Faster R-CNN RPN 단계에서 후보 박스 생성 -> Gliding Vertex 모듈에서 꼭짓점 보정
 
## 2. Model Ensemble
* Gliding Vertex 논문에서는 성능을 높이기 위해 여러 모델을 앙상블 했습니다.
* 구체적으로:
  * 여러 백본(ResNet-101, ResNetXt 등)을 학습시킨 후
  * 예측 결과(OBB)를 합쳐서 성능 향상
* 앙상블은 DOTA 벤치마크 같은 대회에서 흔히 쓰이는 전략
* -> 단일 모델보다 더 안정적이고 mAP 상승 효과 Rotated Object Detection (회전 객체 탐지)
## 1.개념
* 일반적인 객체 탐지(Object Detection)는 수평(Horizontal) 박스(HBB, Horizontal Bounding Box)를 사용합니다.
* 하지만 항공/위성 이미지, OCR(문자 인식), 도로 표지판, 드론 영상 같은 경우
* 물체가 회전된 상태로 많이 등장 -> 수평 박스로는 물체를 정확히 감싸지 못함.
* 그래서 Rotated Bounding Box (OBB, Oriented Bounding Box)를 사용하는 탐지 방법이 연구됨
  * OBB는 중심 좌표(x,y), 폭 w, 높이 h, 각도 θ 로 정의
  * -> 회전된 객체도 정확히 감쌀 수 있음
 
## 2.문제점
* 수평 박스에서 각도를 추가하면 **회전 각도 회귀 문제** 발생 -> 불연속성 문제(예: -90°와 +90°)
* 작은 물체/밀집된 물체에서 예측 안정성이 떨어짐.
* 그래서 여러 연구들이 이 문제를 풀려고 다양한 방법을 제안했습니다.

-----------------------------------------------------------------------------------------------------------------------------------

# Gliding Vertex on the Horizontal Bounding Box (2020)
## 개념
* 기본 아이디어: 회전된 박스를 직접 회귀하지 않고, 수평 박스(HBB)에서 꼭짓점을 **"미끄러뜨려(glide)"** 위치를 보정해서 OBB를 얻자.

## 방식
1. 먼저 일반 Detector로 수평 박스(HBB) 예측
2. 그 박스의 네 꼭짓점(vertex)을 offset(Δx, Δy) 만큼 이동시켜서
   * 물체를 감싸는 회전 박스(OBB) 생성
3. 즉, "HBB + 꼭짓점 이동" 방식으로 간접적으로 회전 박스를 얻

## 장점
* 직접 각도를 회귀하는 방식보다 안정적
* 다양한 각도의 물체도 잘 탐지
* OBB 데이터셋(예: DOTA)에서 좋은 성능

# RBox-CNN (2018)
## 개념
* 기존 Faster R-CNN을 Rotated Box(RBox) 예측 가능하게 확장한 모델
* RBox = (x,y,w,h,θ)

## 방식
1. Region Proposal Network (RPN) 단계에서부터
   * Anchor를 수평이 아닌, 다양한 각도의 Rotated Anchor로 설계
2. Classification + Regression Head 에서
   * 클래스 예측 + (x, y, w, h, θ) 회귀

## 특징
* OBB를 직접 회귀하므로 -> 각도 정보까지 정확히 예측 가능
* 수평 박스 기반 방법보다 더 자연스럽게 회전 객체 탐지

## 단점
* 각도 불연속성 문제(예: -90° vs +90°) 존재
* Anchor 설계가 복잡해짐

-----------------------------------------------------------------------------------------------------------------------------------

# Gliding Vertex on the Horizontal Bounding Box (2020)

## 1. Faster R-CNN with FPN
* Gliding vertex 는 **기본 Detector로 Faster R-CNN + FPN(Feature Pyramid Network)** 을 사용합니다.
* 이유:
  * FPN은 다중 스케일 특징을 활용해 작은 물체까지 잘 탐지할 수 있음
  * 회전 객체 데이터셋(DOTA 등)은 작은 비행기, 배, 차량 등 다양한 크기의 물체가 많기 때문에 FPN이 필수.
* 흐름:
  * Backbone(CNN) => FPN으로 다중 스케일 Feature -> Faster R-CNN RPN 단계에서 후보 박스 생성 -> Gliding Vertex 모듈에서 꼭짓점 보정
 
## 2. Model Ensemble
* Gliding Vertex 논문에서는 성능을 높이기 위해 여러 모델을 앙상블 했습니다.
* 구체적으로:
  * 여러 백본(ResNet-101, ResNetXt 등)을 학습시킨 후
  * 예측 결과(OBB)를 합쳐서 성능 향상
* 앙상블은 DOTA 벤치마크 같은 대회에서 흔히 쓰이는 전략
* -> 단일 모델보다 더 안정적이고 mAP 상승 효과

-----------------------------------------------------------------------------------------------------------------------------------

# RBox-CNN

## 1. Rotated Bounding Box Anchor
* Faster R-CNN의 RPN에서는 보통 수평 Anchor를 씁니다.
* RBox-CNN에서는 각도를 가진 Anchor (RBox Anchor) 를 사용:
  * (x, y, w, h, θ) 형태
  * 다양한 각도 (예: -90°, -60°, …, 0°, …, +90°)를 미리 정의해 Anchor로 둡니다.
* -> 회전된 물체 후보를 더 정확히 잡아낼 수 있음

## 2. Rol Pooling vs RRol Pooling vs DRol Pooling
* Rol Pooling (기존 Faster R-CNN)
  * 수평 박스를 기준으로 Feature Map에서 영역을 잘라 고정 크기로 변환
  * 한계: 물체가 기울어져 있으면 Feature가 깨짐
* RRol Pooling (Rotated Rol Pooling)
  * RBox Anchor에 맞게 Feature Map에서 회전된 영역을 Crop + Pooling
  * 즉, Rol를 회전시켜서 물체 방향과 정렬
  * -> 회전된 물체도 Feature를 잘 보존
* DRol Pooling (Deformable Rol Pooling)
  * 고정된 격자 대신, 학습 가능한 Offset을 적용해 더 유연한 Pooling
  * 즉, 물체 모양(비행기 날개, 긴 배 등)에 맞게 Feature를 "휘어서" 가져옴
  * -> 변형된 물체에도 강건















































































