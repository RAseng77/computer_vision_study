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

## EfficientNet 아키텍처 구조
## 전체 흐름
* EfficientNet은 크게 보면:
  1. Stem (초기 레이어)
  2. 여러 MBConv 블록 (Stage)
  3. Final Head (Pooling + FC Layer)

## Stem (초기 레이어)
* 입력 이미지를 처음 받아 처리하는 부분
* 3x3 Conv (stride=2) -> 채널 32로 확장 -> BatchNorm -> Swish 활성화.
* 이미지 크기를 줄이고 기본 feature map 생성.

## MBConv 블록 (EfficientNet의 핵심)
* EfficientNet 은 **MobileNetV2의 Inverted Residual Block (MBConv)** 을 확장해 사용.
* 구조는 다음과 같아요:
  1. Expansion (1x1 Conv)
     * 입력 채널을 t배 확장 (보통 t=6).
     * 채널 수 확장 후 BN + Swish.
  2. Depthwise Convolution (3x3 or 5x5 Conv)
     * 각 채널별로 독립적인 convolution 수행
     * 파라미터/연산량 크게 절약
     * BN + Swish 적용
  3. Squeeze-and-Excitation(SE) 블록
     * Global Average Pooling -> FC -> ReLU -> FC -> Sigmoid.
     * 채널별 가중치 학습해서 중요 채널 강화, 덜 중요한 채널 억제.
  4. Projection (1x1 Conv)
     * 채널 수를 다시 줄여 projection
     * BN 적용
  5. Skip Connection (조건부)
     * 입력과 출력이 같은 크기일 경우, residual connection 적용
    
  ## Head (출력 부분)
  * 마지막 feature map을 Conv 1x1 으로 1280 채널까지 확장
  * Global Average Pooling으로 (1280,) 벡터 생성.
  * Dropout 적용 후, Fully Connected Layer로 최종 클래스 예측.


















































































