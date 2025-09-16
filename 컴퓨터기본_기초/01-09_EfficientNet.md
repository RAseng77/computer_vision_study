## EfficientNet 이론 정리
### EfficientNet이 나온 배경
* 기존 CNN 모델들(ResNet, DenseNet, Inception 등)은 성능을 높이려면 보통 세 가지 방법을 사용:
  1. Depth (깊게) -> 레이어 수 증가
  2. Width (넓게) -> 채널 수 증가
  3. Resolution (크게) -> 입력 이미지 해상도 증가
* 문제: 각각을 독립적으로 키우면 연산량(FLOPs) 폭발 -> 비효율적
* EfficientNet은 이 세 가지를 균형 있게 스케일업하는 방법을 제안했습니다.

## Compound Scalling (핵심 아이디어)
* EfficientNet은 Depth, Width, Resolution을 동시에 조절하는 Compound Scalling 방식을 도입.
* 기존:
  * ResNet -> 깊이 위주 증가
  * WideResNet -> 폭 위주 증가
  * FixRes -> 해상도만 증가
* EfficientNet:
  * 세 가지를 고정된 비율로 같이 키움
* 수식 (논문 제안)
  * $depth = \alpha^{\phi}, \; width = \beta^{\phi}, \; resolution = \gamma^{\phi}$ 
* 단, 제약조건:
  * $\alpha \cdot \beta^{2} \cdot \gamma^{2} \approx 2$


## EfficientNet 기본 구조
* EfficientNet은 **기본 블록** 으로 MobileNetV2의 MBConv 블록을 사용합니다.
* MBConv (Moblie Inverted Bottleneck Convolution):
  1. 1x1 Conv로 채널 확장 (Expansion)
  2. Depthwise Conv (채널별 COnvolution)
  3. 1x1 Conv로 채널 축소 (Projection)
  4. Skip Connection 적용 (조건부)
* 장점: 연산량 절약 + 성능 유지

## EfficientNet 모델 계열
* EfficientNet-B0: 기본 모델 (φ=0, 약 5.3M 파라미터, 224×224 입력)
* EfficientNet-B1~B7: 점점 더 큰 모델 (φ 증가)
  * B7은 φ=7, 입력 600×600, 성능 매우 좋지만 무겁다.

