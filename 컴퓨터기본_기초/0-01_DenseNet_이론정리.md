## DenseNet 이론 정리
### DenseNet의 아이디어
* 기존 CNN(ResNet 포함)은 각 레이어가 **자기 직전 레이어** 의 출력만 받음
* DenseNet은 **모든 이전 레이어의 출력**(feature map)을 **현재 레이어의 입력으로 연결.**
  * 즉, Dense Connectivity (밀집 연결) 구조.
* 이렇게 하면 특징 재사용(feature reuse)효과 -> 더 적은 파라미터로 더 효율적인 학습 가능.

## DenseNet 구조
* DenseNet은 크게 3가지 블록으로 나뉜다.
### 1. Dense Layer
* Conv -> BN -> ReLU 연산을 통해 새로운 feature map 생성.
* 그 다음, 입력과 새로운 feature map을 채널 방향으로 concat.
* 즉, output = [input, new_features].

### 2. Dense Block
* 여러 Dense Layer를 연속으로 쌓은 구조.
* 각 Layer는 앞선 모든 Layer의 출력을 받아들임.

### 3.Transition Layer
* Dense Block 사이에 들어가는 블록
* 역할:
  * 1x1 Conv(채널 수 줄이기 -> 연산량 감소)
  * 2x2 AvgPooling(해상도 줄이기)

## DenseNet의 성장률
### 1. Feature Reuse
* 이전 레이어의 특징을 모두 활용하므로 중복된 feature 학습을 줄임.
### 2. 효율적 파라미터 사용
* ResNet보다 적은 파라미터로도 비슷하거나 더 좋은 성능.
### 3. Gradient Flow 개선
* 입력이 계속 concat으로 이어지므로, gradient가 깊은 레이어까지 잘 전달됨.
