# YOLOv1 핵심 아이디어
    이름처럼 You Only Look Once -> 이미지를 한 번만 보고 "어디에 무엇이 있는지" 바로 예측하는 모델이다.

  예전 방식(R-CNN, Fast R-CNN 등)은 이미지를 잘라서 여러 번 봤는데, YOLO는 그냥 한 방에 끝냅니다.

## 어떻게 동작할까?
1. 이미지를 격자로 나눔
     1. 예: 448 x 448 이미지를 7 x 7 칸으로 나눠요.
     2. 각 칸은 자기 안에 있는 물체의 중심을 책임져요.
2. 각 칸이 예측하는 것
     1. 바운딩 박스 2개 (위치와 크기)
          -> 중심 좌표 (x, y), 폭(w), 높이(h), 신뢰도(confidence)
     2. 물체의 종류 (강아지, 고양이, 자동차... 클래스 확률)
3. 최종 출력
   1. 7 x 7 격자 x (박스 2개 + 클래스 확률) = 1470개 값이 한 번에 나와요


## 네트워크 구조 (간단 버전)
1. CNN(합성곱 신경망)으로 특징을 뽑음 (사진의 패턴, 윤곽, 색상 등).
2. 마지막에 FC(완전연결층)로 1470개의 값을 예측.
3. 중간에는 ReLU 대신 Leaky ReLU라는 활성화 함수 사용.
4. 드롭아웃으로 과적합 방지.

## 예측 결과 해석
1. 각 박스마다 Confidence(신뢰도) = "이 칸에 물체가 있을 확률 x 예측 박스가 실제랑 얼마나 겹치는지(IoU)"
2. 물체 종류 확률과 곱해서 "자동차 점수","고양이 점수" 처럼 나옴.
3. 마지막엔 "NMS(Non-Max Suppression)"로 겹치는 박스 정리.
--------------------------------------------------------------------------------------------------------------------

# YOLOv2 핵심 아이디어 
    YOLOv1은 빨랐지만, 작은 물체 탐지에 약하고 정확도가 부족했어요. 그래서 YOLOv2에서는 더 정확하면서도 여전히 빠른 모델을 목표로 했습니다.

## YOLOv2의 주요 개선점
1. Anchor Box 도입
   1. YOLOv1은 직접 (x,y,w,h)를 회귀했지만 → YOLOv2는 **미리 정해둔 박스(Anchor)**를 기준으로 보정.
   2. → 다양한 크기·비율의 물체를 잘 잡음.
2. Batch Normalization
   1. 모든 합성곱 층에 BN 적용 → 학습 안정 + 수렴 속도 ↑ + 정확도 ↑.
3. 더 깊고 강한 Backbone (Darknet-19)
   1. 19개의 Conv + 5개의 MaxPool → 더 좋은 특징 추출.
   2. ImageNet에서 미리 학습 → 탐지에 활용.
4. High-Resolution Classifier
   1. 분류 네트워크도 448×448 고해상도로 학습 → 작은 물체도 더 잘 인식.
5. Passthrough Layer (Skip Connection)
   1. 낮은 단계(해상도 큰 feature map)의 정보를 뒤쪽(작은 feature map)으로 가져옴.
   2. → 작은 물체 탐지 성능 개선.
6. 멀티스케일 학습
   1. 학습 도중 이미지 크기를 랜덤으로 바꿈 (320~608, 32단위).
   2. → 다양한 해상도에서 잘 작동하는 모델.
7. YOLO9000 (확장판)
   1. COCO(탐지 데이터) + ImageNet(분류 데이터)를 동시에 학습.
   2. 9000개 클래스까지 탐지가 가능해짐! (WordTree라는 계층적 분류 체계 사용)
  
## YOLOv2 동작 방식
1. 이미지를 Backbone(Darknet-19)으로 특징 추출
2. Anchor Box를 기준으로 각 격자 셀이 여러 후보 박스를 예측
3. Confidence(신뢰도)와 클래스 확률을 곱해 "자동차일 확률 x 박스 정확도" 같은 점수 계산
4. NMS로 겹치는 박스 제거 -> 최종 탐지

## 한 줄 요약
YOLOv2는 Anchor Box와 더 강한 네트워크(Darknet-19), BN, Passthrough, 멀티스케일 학습을 도입해 YOLOv1보다 정확도↑ + 작은 물체 인식력 개선에 성공한 모델입니다.
--------------------------------------------------------------------------------------------------------------------
# YOLOv3 핵심 아이디어   
    YOLOv2까지는 좋아졌지만, 
    1. 작은 물체 탐지
    2. 정확도(특히 mAP@0.5 이상)
    이 여전히 부족했어요.
    그래서 YOLOv3는 더 깊고 강한 백본과 다중 스케일 탐지로 이를 해결했습니다.

## YOLOv3 주요 특징 
1. 더 강력한 Backbone (Darknet-53)
   1. ResNet 아이디어를 차용 → Residual Block 사용.
   2. 53개의 Conv 층 → 깊지만 효율적.
   3. ImageNet Top-1/Top-5 정확도에서 ResNet-101에 필적하면서도 더 빠름.
2. 다중 스케일 탐지 (Feature Pyramid 구조)
   1. YOLOv2까지는 1개의 해상도에서만 탐지.
   2. YOLOv3는 **3가지 스케일(13×13, 26×26, 52×52)**에서 동시에 탐지.
   3. 작은 물체는 52×52에서, 큰 물체는 13×13에서 탐지 → 작은 물체 성능 개선.
3. Anchor Box 유지 + 개선
   1. 여전히 Anchor Box 사용.
   2. k-means 클러스터링으로 최적 Anchor 추출.
   3. 각 스케일별로 Anchor를 나누어 배치.
4. 클래스 예측 방식 변경
   1. Softmax 대신 Independent Logistic Classifier (Sigmoid) 사용.
   2. → 멀티라벨(한 물체가 여러 클래스 가능)도 지원.
5. Objectness Score
   1. 각 Anchor마다 "물체일 확률"(objectness)을 Sigmoid로 예측.
   2. 물체 존재 여부를 더 명확히 반영.

## YOLOv3 동작 방식
1. 이미지를 Darknet-53으로 통과시켜 특징 추출
2. 추출된 Feature Map에서 3단계 해상도(13, 26, 52)에서 Anchor Box 기반 탐지
3. 각 박스는 (x, y, w, h, objectness, 클래스 확률)을 출력.
4. NMS로 겹치는 박스 제거 -> 최종 탐지 결과.

## 한 줄 요약
YOLOv3 는 Residual 기반 백본(Darknet-53) + 다중 스케일 탐지(FPN)로 작은 물체 인식 성능을 크게 높인 YOLO 버전입니다.
--------------------------------------------------------------------------------------------------------------------
# YOLOv4 핵심 아이디어
YOLOv3까지는 성능이 괜찮았지만, 더 높은 정확도 + 더 빠른 속도가 필요했습니다. 그래서 YOLOv4는 다양한 최신 기법들을 적절히 조합해서 속도와 정확도를 잡은 모델을 만들었습니다.

## YOLOv4 주요 특징 
1. 새로운 Backbone - CSPDarknet-53
   1. YOLOv3의 Darknet-53을 개선
   2. CSP (Cross Stage Partial connection) 구조 사용 -> 계산 효율↑, 성능↑.
   3. 더 빠르면서도 정확도가 높음
2. Neck 부분 강화 (Feature Fusion)
   1. SPP (Spatial Pyramid Pooling) -> 다양한 크기의 리셉티브 필드 확보
   2. PANet (Path Aggregation Network) -> FPN보다 더 효과적인 상,하위 특징 결합
   3. -> 작은 물체도 잘 잡고, 큰 물체도 안정적 탐지.
3. Head (Detection Part)
   1. YOLOv3와 비슷하게 Anchor 기반으로 여러 스케일에서 탐지
   2. 3가지 크기의 Feature Map에서 박스를 예측
4. 학습 기법 (Bag of Freebies & Specials)
YOLOv4는 새로운 구조를 만든 것보다, 학습 과정에 최신 기법을 잔뜩 끌어왔어요.
    1. Bag of Freebies (성능↑, 속도 영향 없음)
       1. Data Augmentation: Mosaic, CutMix
       2. Self-Adversarial Training (SAT)
       3. DropBlock, Label Smoothing
    2. Bag of Specials(성능↑, 속도에 조금 영향)
       1. Mish 활성화 함수
       2. IoU Loss (CIOU)
       3. CmBN (Cross mini-Batch Normalization)

## YOLOv4 동작 방식
1. 입력 이미지를 CSPDarknet-53으로 통과 -> 강력한 특징 추출.
2. SPP + PANet Neck에서 여러 스케일의 특징을 합침.
3. Head에서 Anchor 기반으로 3단계 스케일(큰 물체 ~ 작은 물체) 탐지.
4. 최종적으로 Confidence + Class 확률 계산 -> NMS로 정리

## 한 줄 요약
YOLOv4는 CSPDarknet-53 + SPP + PANet에 최신 학습 트릭(Mosaic, CIOU, Mish 등)을 더해 빠르고 정확한 실무형 객체 탐지기를 완성한 버전입니다.
--------------------------------------------------------------------------------------------------------------------
# YOLOv5 핵심 아이디어
YOLOv4가 논문으로 발표된 것과 달리, YOLOv5는 공식 논문 없이 Ultralyics 팀이 PyTorch로 구현해서 공개한 버전입니다.

## YOLOv5 주요 특징
1. 구조 (Backbone - Neck - Head)
   1. Backbone: CSPDarknet 변형 (CSP 구조로 계산 효율↑)
   2. Neck:PANet + SPPF (Spatial Pyramid Pooling - Fast)
   3. Head: Anchor 기반 탐지 (여전히 3스케일 예측)
2. 모델 크기 버전 제공
   1. 4가지 크기(small, medium, large, x-large -> v5s, v5m, v5l, v5x)
   2. → 속도·정확도 원하는 대로 선택 가능.
3. 학습 & 배포 친화적
   1. PyTorch 기반으로 쉽게 학습 가능.
   2. ONNX, CoreML, TensorRT 등 다양한 환경으로 간단히 변환 가능
   3. AutoAnchor, AutoBash, Hyperparameter Evolution 같은 기능 내장
4. Loss 함수
   1. GIoU/CIoU 등 IoU 기반 손실 사용
   2. Objectness(물체 존재 여부)









