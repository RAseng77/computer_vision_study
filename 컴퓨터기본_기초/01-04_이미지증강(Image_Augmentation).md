## 이미지 증강 (Image Augmentation) 정리
### 1. 이미지 증강 개요
* 정의: 데이터셋의 양과 다양성을 인위적으로 늘리는 기법
* 목적:
  * 과적합(Overfitting) 방지
  * 모델의 일반화 능력(새 데이터에서도 잘 작동)을 강화
* 쉽게 말해: "같은 사진을 조금씩 다르게 바꿔서 여러 장처럼 학습시키자."

### 2. 대표적인 증강 방법
* 기하학적 변환
  * 회전(Rotation), 뒤집기(Flip), 이동(Shift), 크기 조절(Resize Crop)
* 픽셀 기반 변환
  * 밝기/대비 조절, 색상 변화, 감마 보정
* 노이즈/블러
  * Gaussian 노이즈, Blur, JPEG 압축 등
* 고급 기법
  * CutMix: 두 이미지를 섞어서 일부 영역 교체
  * MixUp: 두 이미지를 섞어 새로운 샘플 생성
  * CutBlur: 일부 영역만 블러 처리
 
### 3. 이미지 증강의 효과
* 연구 결과, 증강을 적용하면:
  * 평균 정밀도(AP) 증가
  * 학습 손실(Train Loss) 감소
  * 검증 정확도(Validation Accuracy) 상승
* 즉, "데이터를 다양하게 보여주면 모델이 더 똑똑해진다."

### 4. PyTorch의 torchvision.transforms.v2
* PyTorch 내장된 증강 도구
* 주요 기능:
  * AutoAugment: 자동으로 증강 전략을 찾아줌
  * RandAugment, TrivialAugmentWide, AugMix: 간단/랜덤/혼합형 자동 증강 기법
  * CutMix / MixUp: 데이터 혼합 계열 기법
```
from torchvision.transforms import v2

transform = v2.Compose([
  v2.RandomHorizontalFlip(),
  v2.RandomRotation(15),
  v2.ColorJitter(brightness=0.2, contrast=0.2),
  v2.ToTensor()
])
```

### 5. imgaug 라이브러리
* 파이썬에서 가장 널리 쓰이는 증강 라이브러리 중 하나.
* 장점:
  * 이미지뿐 아니라 **히트맵, 세그멘테이션 맵, 키포인트, 바운딩박스** 도 같이 증강 가능.
* 예시:
```
import imgaug.augmenters as iaa

seq = iaa.Sequential([
  iaa.Fliplr(0.5),
  iaa.Affine(rotate=(-20, 20)),
  iaa.Multiply((0.8, 1.2))
])
```

### 6. 다른 라이브러리들과 비교
* Albumentations: 속도와 기능 최적화
* imgaug: 유연하고 다양한 기능 제공
* torchvision: PyTorch 공식, 통합성 좋음
* Augmentor, solt: 특정 기능에 특화된 가벼운 라이브러리











