## Super Resolution (SR)
### 1. 정의
* Super Resolution은 저해상도(LR, Low-Resolution) 이미지를 고해상도(HR, High-Resolution) 이미지로 복원하는 문제입니다.
* 예를 들어 작은 썸네일 이미지를 선명하게 확대하거나, 흐릿한 CCTV 영상을 선명하게 만드는 것이 대표적인 응용입니다.

### 2. 문제 정의: LR -> HR 복원 문제
* 입력: 저해상도 이미지 (정보가 부족하고 픽셀이 뭉개진 상태)
* 출력: 고해상도 이미지 (원본과 최대한 비슷하게 복원)
* -> 이 문제는 정보가 손실된 상태에서 복원해야 하기 때문에 정답이 유일하지 않고 Ill-posed Problem에 해당합니다.

### 3. Ill-posed Problem (불량정의 문제, Regular Inverse Problem)
* Well-posed 문제(수학적 정의):
  1. 해가 존재한다.
  2. 해가 유일하다.
  3. 입력이 조금 변하면 해도 조금만 변한다(안정성).
* Super Resolution은 ill-posed:
  1. LR 이미지에서 여러 개의 HR 후보가 나올 수 있음(해의 비유일성).
  2. 예: 16x16 얼굴 이미지를 128x128로 복원할 때, 여러 plausible(그럴듯한) 얼굴이 가능
  3. 따라서 Regularization이나 Prior를 도입해야 함 (예: CNN 학습, GAN 기반 생성).
