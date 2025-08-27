# Mask R-CNN

## 1. Instance Segmentation
* Semantic Segmentation: 픽셀 단위로 클래스만 구분 (예: 고양이 픽셀 vs 배경 픽셀) -> 같은 클래스는 전부 하나로 취급
* Instance Segmentation: 같은 클래스라도 개별 객체(instance)를 구분해야 함.
  * 예: 고양이 2마리가 있으면 -> "고양이 A"와 "고양이 B"로 각각 마스크 출력
* Mask R-CNN은 대표적인 Instance Segmentation 모델 입니다.

## 2. Mask R-CNN (2017, Facebook AI Research)
* Faster R-CNN을 기반으로, 픽셀 단위 마스크 예측 branch를 추가한 모델
* 출력:
  * 1. 클래스 레이블 (고양이, 사람, 자동차 ...)
  * 2. 바운딩 박스 좌표 (x, y, w, h)
  * 3. 픽셀 단위 마스크 (instance Mask)
* **"탐지 + 분류 + 세그맨테이션"** 을 동시에 수행하는 네트워크

## 3. Architecture (구조 흐름)
1. Backbone (CNN, 보통 ResNet + FPN)
 * 입력 이미지에서 Feature Map 추출
2. Region Proposal Network (Proposal Layer, RPN)
 * Anchor 기반으로 "물체 후보 박스(Region Proposal)" 생성.
3. RoI Align
 * Proposal을 Feature Map 위에서 잘라내고 고정 크기로 변환
 * -> 이후 분류기/회귀기/마스크 분지로 전달
4. Heads
 * Box Heads: 클래스 분류 + 박스 좌표 회귀
 * Mask Branch: 픽셀 단위 마스크 예측

## 4. Proposal Layer (RPN, Region Proposal Network)
* Faster R-CNN에서 도입된 RPN 그대로 사용
* 특징 맵 위에서 Anchor Box를 두고:
  * Objectness Score (물체인지 아닌지)
  * Bounding Box Regression (위치 보정)
 * 상위 N개의 후보 영역(Region Proposal)을 추출
 * Mask R-CNN의 Downstream Task에 입력으로 전달

## 5. RoI Pooling vs RoI Align
* RoI Pooling (기존 Faster R-CNN)
  * Region Proposal 영역을 고정 크기로 맞추는 과정에서 **양자화(Quantization)** 를 사용
  * 픽셀 정렬이 조금 깨짐 -> 마스크 예측 시 픽셀 단위 정확도 떨어짐
* RoI Align (Mask R-CNN에서 새롭게 제안)
  * Quantization을 제거하고, bilinear interpolation을 사용해 정확히 Feature Map 값을 샘플링
  * 픽셀 정렬 오차가 사라져서 마스크 예측이 훨씬 정확해짐
 * Mask R-CNN의 성능 향상의 핵심 비밀 중 하나가 RoI Align

## 6. Mask Branch
* RoI Align으로 뽑아온 Feature -> Mask Branch에 입력
* Mask Branch = 작은 FCN (Fully Convolutional Network)
* 각 RoI에 대해 K개의 마스크(클래스 개수만큼)를 예측
  * 예: 80 클래스 -> 80개의 마스크 채널
  * 최종적으로 해당 RoI의 클래스에 맞는 채널의 마스크만 선택
* 출력은 바운딩 박스 안에서의 픽셀 단위 분할 결과 

## 요약 정리
* Instance Segemtation -> 같은 클래스 객체도 개별적으로 마스크 출력Mask R-CNN

## 1. Instance Segmentation
* Semantic Segmentation: 픽셀 단위로 클래스만 구분 (예: 고양이 픽셀 vs 배경 픽셀) -> 같은 클래스는 전부 하나로 취급
* Instance Segmentation: 같은 클래스라도 개별 객체(instance)를 구분해야 함.
  * 예: 고양이 2마리가 있으면 -> "고양이 A"와 "고양이 B"로 각각 마스크 출력
* Mask R-CNN은 대표적인 Instance Segmentation 모델 입니다.

## 2. Mask R-CNN (2017, Facebook AI Research)
* Faster R-CNN을 기반으로, 픽셀 단위 마스크 예측 branch를 추가한 모델
* 출력:
  * 1. 클래스 레이블 (고양이, 사람, 자동차 ...)
  * 2. 바운딩 박스 좌표 (x, y, w, h)
  * 3. 픽셀 단위 마스크 (instance Mask)
* **"탐지 + 분류 + 세그맨테이션"** 을 동시에 수행하는 네트워크

## 3. Architecture (구조 흐름)
1. Backbone (CNN, 보통 ResNet + FPN)
 * 입력 이미지에서 Feature Map 추출
2. Region Proposal Network (Proposal Layer, RPN)
 * Anchor 기반으로 "물체 후보 박스(Region Proposal)" 생성.
3. RoI Align
 * Proposal을 Feature Map 위에서 잘라내고 고정 크기로 변환
 * -> 이후 분류기/회귀기/마스크 분지로 전달
4. Heads
 * Box Heads: 클래스 분류 + 박스 좌표 회귀
 * Mask Branch: 픽셀 단위 마스크 예측

## 4. Proposal Layer (RPN, Region Proposal Network)
* Faster R-CNN에서 도입된 RPN 그대로 사용
* 특징 맵 위에서 Anchor Box를 두고:
  * Objectness Score (물체인지 아닌지)
  * Bounding Box Regression (위치 보정)
 * 상위 N개의 후보 영역(Region Proposal)을 추출
 * Mask R-CNN의 Downstream Task에 입력으로 전달

## 5. RoI Pooling vs RoI Align
* RoI Pooling (기존 Faster R-CNN)
  * Region Proposal 영역을 고정 크기로 맞추는 과정에서 **양자화(Quantization)** 를 사용
  * 픽셀 정렬이 조금 깨짐 -> 마스크 예측 시 픽셀 단위 정확도 떨어짐
* RoI Align (Mask R-CNN에서 새롭게 제안)
  * Quantization을 제거하고, bilinear interpolation을 사용해 정확히 Feature Map 값을 샘플링
  * 픽셀 정렬 오차가 사라져서 마스크 예측이 훨씬 정확해짐
 * Mask R-CNN의 성능 향상의 핵심 비밀 중 하나가 RoI Align

## 6. Mask Branch
* RoI Align으로 뽑아온 Feature -> Mask Branch에 입력
* Mask Branch = 작은 FCN (Fully Convolutional Network)
* 각 RoI에 대해 K개의 마스크(클래스 개수만큼)를 예측
  * 예: 80 클래스 -> 80개의 마스크 채널
  * 최종적으로 해당 RoI의 클래스에 맞는 채널의 마스크만 선택
* 출력은 바운딩 박스 안에서의 픽셀 단위 분할 결과 

## 요약 정리
* Instance Segemtation: 같은 클래스 객체도 개별적으로 마스크 출력
* Mask R-CNN: Faster R-CNN + Mask Branch, 탐지 + 분류 + 마스크 동시에
* Architecture: Backbone -> RPN -> RoI Align -> Box Head + Mask Branch
* Proposal Layer (RPN): Anchor 기반 Region Proposal 생성
* RoI Pooling: Quantization으로 픽셀 오차 발생
* RoI Align: Interpolation 기반 -> 정밀 픽셀 정렬, Mask 성능↑
* Mask Branch: FCN구조, 각 RoI별 픽셀 단위 마스크 예측

## 한 줄 요약
* Mask R-CNN은 Faster R-CNN에 Mask Branch와 RoI Align을 추가해
* **"객체 탐지 + 인스턴스 세그맨테이션"** 을 동시에 수행하는 강력한 프레임워크입니다.
































































































