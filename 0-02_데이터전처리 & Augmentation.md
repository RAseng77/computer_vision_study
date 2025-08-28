# Image Preprocessing
* Image Data의 전처리
* 전처리 예시
  * Resize
  * Zero Centering
  * Scailing
  * Contrast Normalization - GCN, LCN

# Data Augmentation
* 데이터 증강
* 원본 데이터 (이미지)에 임의의 변화를 주어 데이터를 생성하는 작업
* 데이터 증강의 효과
  * 데이터의 양과 다양성을 키움
  * 모델의 성능을 향상시킴
  * Overfitting 방지
 
## Radom Crop
```
transform = transforms.RandomCrop(224);
```

## RandomHorizontalFlip()
* 이미지를 좌우로 뒤집음. 대칭 구조(예: 사람, 물체)에 강건한 학습을 위해 사용.
```
transform = transforms.RandomHorizontalFlip(p=0.3)
```

## RandomVerticalFlip()
* 이미지를 상하로 뒤집음. 데이터셋에 상하 대칭성이 있을 때 유용.
```
transform = transforms.RandomVerticalFlip()
```

## RandomRotation
* 이미지를 무작위 각도로 회전. 특정 각도 변화에 둔감한 모델을 만들기 위해 사용.
```
transform = transforms.RandomRotation(degrees=(-30, 30))
```

## RandomAffine
* 이미지를 아핀 변환(회전, 이동, 스케일, 시어링 등). 복잡한 위치 변화에 강건한 모델 학습에 도움.
```
transform = transforms.RandomAffine(degrees=0, translate=(0.1, 0.1))
```

## ColorJitter
* 이미지의 밝기, 대비, 채도, 색조를 무작위로 변경. 컬러 변화에 둔감한 모델을 만들기 위해 사용.
```
transform = transforms.ColorJitter(brightness = (0.8, 0.9),
contrast = (0.4, 0.8), saturation=(0.7,0.9), hue=(-0.2, 0.2))
```

## RandomAdjustSharpness - Blur
* 이미지의 선명도를 무작위로 조절. Blur 효과를 통해 다양한 화질에 적응 가능.
```
transform = transforms.RandomAdjustSharpness(0, p=0.5)
```

## Data Augmentation 종류
* RandomChoice 여러 변환 중 하나를 무작위로 선택해서 적용.
* RandomApply 여러 변환을 묶어서 확률적으로 적용.
* AutoAugment 사전 정의된 정책 기반 자동 증강 기법. ImageNet, CIFAR10, SVHN 등 데이터셋별 추천 정책 제공.
```
transform = transforms.RandomChoice([
 transforms.RandomHorizontalFlip(),
 transforms.RandomRotation(degrees=(-30, 30)),
])

transform = transforms.RandomApply([
 transforms.RandomHorizontalFlip(),
 transforms.RandomRotation(degrees=(-30,30)),
], p=0.5)

transform = transforms.AutoAugment(transforms.AutoAugmentPolicy.IMAGENET)
```








































