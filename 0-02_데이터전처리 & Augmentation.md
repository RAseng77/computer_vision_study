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
```
transform = transforms.RandomHorizontalFlip(p=0.3)
```

## RandomVerticalFlip()
```
transform = transforms.RandomVerticalFlip()
```

## RandomRotation
```
transform = transforms.RandomRotation(degrees=(-30, 30))
```

## RandomAffine
```
transform = transforms.RandomAffine(degrees=0, translate=(0.1, 0.1))
```

## ColorJitter
```
transform = transforms.ColorJitter(brightness = (0.8, 0.9),
contrast = (0.4, 0.8), saturation=(0.7,0.9), hue=(-0.2, 0.2))
```

## RandomAdjustSharpness - Blur
```
transform = transforms.RandomAdjustSharpness(0, p=0.5)
```

## Data Augmentation 종류
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









































