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
