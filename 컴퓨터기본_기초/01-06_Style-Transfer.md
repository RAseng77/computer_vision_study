## Style Transfer (스타일 변환)

## 1. 개념
* Style Transfer는 한 이미지의 **스타일(화풍)** 을 다른 이미지의 **콘텐츠(구조,형태)** 에 적용하는 기술이에요.
* 예를 들어, 고흐의 그림 스타일을 내 사진에 적용하면, 내 사진이 고흐 그림처럼 변환됩니다.
* 대표적으로 **Neural Style Transfer (NST, 신경망 스타일 변환)** 이 가장 많이 쓰입니다.

## Neural Style Transfer (NST) 정리
## 1. Architecture (구조)
* 기본 아이디어:
  * 사전 학습된 CNN (보통 VGG-19)을 **특징 추출기(Feature Extractor)** 로 사용
  * 세가지 입력:
    1. Content image (콘텐츠 이미지, ex: 사진)
    2. Style image(스타일 이미지, ex: 고흐 그림)
    3. Generated image (결과 이미지, 처음에는 콘텐츠 이미지 or 랜덤 노이즈에서 시작)
  * 결과 이미지를 학습 파라미터로 두고 Backpropagation으로 픽셀 값을 업데이트
* 흐름:
  1. Content / Style / Generated 이미지 모두 CNN에 통과
  2. Content Feature와 Style Feature 추출
  3. Loss 계산 (Content + Style + (optionally) Total Variation)
  4. 결과 이미지의 픽셀 업데이트 (Gradient Descent)

## 2. Loss function for Neural Style Transfer
## (1) Content Loss
* 콘텐츠 이미지를 유지하기 위해
* $L_{content} = \tfrac{1}{2} \sum_{i,j} (F_{ij}^{generated} - F_{ij}^{content})^2$
* 여기서 Fij는 CNN 특정 레이어에서 추출된 feature map 값

## (2) Style Loss
* 스타일 이미지를 재현하기 위해 Gram Matrix 사용 (특징 간 상관관계)
* Gijl​=∑k​Fikl​Fjkl​
* l: 레이어 번호
* Style Loss: $L_{style} = \sum_{l} w_l \tfrac{1}{4N_l^2 M_l^2} \sum_{i,j} (G_{ij}^{generated} - G_{ij}^{style})^2$

## (3) Total Variation Loss (선택적)
* 결과 이미지가 너무 거칠지 않도록 스무딩 역할
* $L_{tv} = \sum_{i,j} \big( (x_{i,j} - x_{i+1,j})^2 + (x_{i,j} - x_{i,j+1})^2 \big)$

## (4) 최종 Loss
* $L_{total} = \alpha L_{content} + \beta L_{style} + \gamma L_{tv}$
* α: 콘텐츠 가중치
* β: 스타일 가중치
* γ: Total Variation 가중치

## 3. Reconstruction of each layer for Neural Style Transfer
* CNN의 계층적 표현을 활용
  * 하위 레이어: 저수준 특징 (엣지, 색상, 질감) -> 스타일과 관련
  * 상위 레이어: 고수준 특징 (사물 구조, 형태) -> 콘텐츠와 관련
* 특정 레이어의 activation을 사용해 이미지 재구성 가능:
  * Content reconstruction -> 높은 레이어 특징을 그대로 두고 재구성
  * Style reconstruction -> 여러 레이어의 Gram Matrix를 기반으로 재구성
* -> 이 과정을 통해 CNN이 어떤 정보를 어디에 저장하는지 직관적으로 볼 수 있음.

## 4. Perceptual Losses for Neural Style Transfer (Network Weight Update)
* NST에서는 네트워크 가중치가 아니라 결과 이미지의 픽셀 값을 업데이트함.
* 즉:
  * CNN (예: VGG-19)은 고정
  * Generated Image만 Gradient Descent로 업데이트
* 이때 사용되는 손실이 바로 Perceptual Loss
  * Content Loss와 Style Loss를 합친 것
  * 사람이 인식하는 **지각적 품질(perceptual quality)** 을 반영
* Optimization 방식:
  * Adam 또는 L-BFGS 같은 Optimizer 사용
  * iteration마다 결과 이미지가 점점 스타일화됨
 
## 요약
* Architecture: 사전학습 CNN -> Generated Image 최적화
* Loss Fuction: Content Loss + Style Loss (+ TV Loss)
* Layer Reconstruction: CNN의 층별 표현에서 Content/Style 분리
* Perceptual Loss: 네트워크 가중치는 고정, 결과 이미지 픽셀만 업데이트
* Results: Original NST(느림) -> Fast NST(실시간) -> AdalN(유연) -> GAN 기반(강력)


















































	​
