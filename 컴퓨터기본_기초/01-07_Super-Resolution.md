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
 
### 4. Interpolation-based SISR (전통적 방법)
* 초기 Super Resolution은 단순한 보간법(interpolation)으로 접근했습니다.
  * Nearest Neighbor: 가장 가까운 픽셀 값으로 채움 (블록 계단 현상).
  * Bilinear: 인접한 2D 주변 픽셀의 선형 보간.
  * Bicubic: 주변 16픽셀 기반 3차 보간 -> Bilinear보다 부드럽고 자연스러움.
  * Lanczos: sinc 함수를 기반으로 한 고급 보간법 -> aliasing 줄이는데 효과적.
* 장점: 빠르고 간단함.
* 단점: 세부 디테일(텍스처, 경계선)을 제대로 복원하지 못함 -> "뭉개짐" 발생.

### 5. Deep Learning-based SISR
* 2014년 Dong et al.의 SRCNN 논문 이후 CNN을 이요한 Super Resolution이 본격적으로 연구됨.
* 딥러닝 기반 방법은 단순한 확대가 아니라 저해상도 -> 고해상도 매핑을 학습합니다.
* 주요 발전 흐름:
  * SRCNN (2014): 최초의 CNN 기반 초해상도
  * VDSR (2016, CVPR): 깊은 네트워크(20 + 레이어)를 활용해 높은 PSNR 성능 달성.
  * EDSR (2017, CVPR): 불필요한 모듈 제거 + residual block 개선으로 SOTA 달성.
  * SRGAN (2017, CVPR): GAN을 도입하여 더 실제같은 텍스처 복원 (perceptual quality ↑).
  * 최근에는 Transformer 기반 모델(SinlR, 2021)등도 활발히 연구

### 6. MSE-based VS GAN-based 접근
* MSE 기반 (Mean Squeared Error Loss)
  * HR과 SR 이미지의 픽셀 차이를 최소화
  * 장점: 높은 PSNR(수치적 정확도).
  * 단점: 결과가 매끄럽지만 디테일이 사라져 "over-smoothed" 현상 발생.
* GAN 기반 (Adversarial Loss)
  * 판별자(Discriminator)와 경쟁하며 사람 눈에 진짜 같은 고해상도 이미지를 생성.
  * 장점: 질감, 텍스처, 디테일이 사실적으로 복원됨.
  * 단점: PSNR은 낮을 수 있음, 가짜 디테일이 생기거나 학습 불안정.
* 요약: MSE 기반 = 수치적으로 정확 / GAN 기반 = 시각적으로 사실적

### 7. Evaluation Metrics (평가 지표)
* PSNR (Peak Signal-to-Noise Ratio): 가장 많이 쓰이는 수치적 지표, 픽셀 차이 기반.
* SSIM (Structural Similarity Index): 구조적 유사성을 반영 (사람의 인식과 좀 더 가까움).
* LPIPS (Learned Perceptual Image Patch Similarity): 딥러닝 feature 기반 인식적 유사도
* 주관적 평가 (MOS, Mean Opinion Score): 사람이 직접 본 만족도 평가.

### VDSR (Accurate Image Super-Resolution Using Very Deep Convolutional Networks, CVPR 2016)
* Kim et al., 2016.
* 20개 이상의 깊은 CNN 레이어 사용 -> 더 복잡한 매핑 학습 가능.
* Residual learning: 저해상도 입력에서 HR 이미지를 직접 예측하는 대신 **잔차(residual, HR-LR)** 를 학습 -> 학습 안정성과 성능 개선.
* 당시 기준으로 매우 높은 PSNR 성능 기록.
* 한계: 학습 시간이 길고, 매우 깊은 네트워크라 메모리 소모가 크다.

### 요약
* Super Resolution은 LR 이미지를 HR로 복원하는 문제이며, 본질적으로 ill-posed.
* 전통적 보간법은 단순하지만 디테일 부족.
* 딥러닝 기반 방법은 CNN, GAN, Transformer로 발전하며 큰 성능 향상.
* MSE 기반은 PSNR 위주, GAN 기반은 perceptual quality 위주.
* **VDSR (2016)** 은 매우 깊은 CNN과 residual learning을 통해 성능을 끌어올린 중요한 milestone.



















































