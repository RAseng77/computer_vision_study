# DETR(DEtection TRansformer) 핵심 아이디어
기존 객체 탐지 모델(YOLO, Faster R-CNN, SSD 등)은 **"앵커(anchor)"** 나 **"후처리(NMS)"** 가 꼭 필요했어요.
하지만 DETR은 이런 복잡한 절차를 없애고, **Transformer(트랜스포머)** 를 이용해 객체 탐지를 순수 엔드-투-엔드(end-to-end) 문제로 바꿔버린 모델입니다.
> 한마디로: "Transformer로 앵커도, NMS도 필요 없는 객체 탐지기"

## DETR 구조
DETR의 구조는 크게 3부분으로 나뉩니다:
1. Backbone (CNN, 예: ResNet-50/101)
   * 입력 이미지를 CNN에 통과시켜 Feature Map을 추출합니다.
2. Transformer Encoder-Decoder
   * Encoder: CNN이 뽑은 Feature Map을 1D 시퀀스로 변환해 Transformer Encoder에 넣습니다. (위치 정보를 위해 Positional Encoding 추가)
   * Decoder: "Object Query" 라는 학습 가능한 토큰을 입력으로 받아, Encoder에서 온 Feature와 어텐션(attention)을 주고 받으며 "이 이미지에 어떤 객체가 있는지" 를 하나씩 예측합니다.
   * 각 Object Query = "한 개의 물체 후보"라고 생각하면 됨.
3. Prediction Head (FFN)
   * Transformer Decoder 출력 -> 작은 MLP를 거쳐서
     * 클래스 확률 (고양이, 사람, 자동차 등)
     * 바운딩 박스 좌표 (x,y,w,h)
   * 를 직접 예측합니다.

## 학습 방법 (Hunfaian Matching)
* DETR은 출력 개수(Object Query)가 고정 (예: 100개) 입니다.
* 하지만 실제 이미지 속 물체 개수는 다 다르죠.
* 그래서 Hungarian Matching 알고리즘으로 "예측 박스 ↔ 실제 GT 박스"를 최적으로 매칭합니다.
* 매칭된 쌍만 손실(loss)에 반영 -> 나머지는 "배경(∅)" 클래스로 처리.

## Loss 함수
* 클래스 예측: Cross-Entropy Loss
* 박스 예측: L1Loss + GIoU Loss
* -> 위치와 크기를 정밀하게 학습

## 한 줄 요약
DETR은 Transformer Encoder-Decoder + Object Query를 이용해서, **"앵커와 NMS 없는 순수 엔드-투-엔드 객체탐지기"** 를 구현한 혁신적인 모델입니다.

# 1.Optimal Matching (최적 매칭)
* DETR은 Decoder가 항상 고정된 개수(예:100개) Object Query를 출력합니다.
* 실제 이미지 속 객체는 100개보다 훨씬 적죠. (예: 7개만 있음)
* 그래서 예측 결과 100개 ↔ 실제 객체 7개를 최적으로 짝지어야(loss 계산용) 합니다.
* 이때 쓰는 게 Optimal Matching 입니다.
* 기준: "클래스 예측 오차 + 바운딩 박스 오차"가 최소가 되도록 GT와 예측을 매칭

# 2. Bounding Box Loss (박스 손실)
DETR은 박스를 직접 예측하므로, 위치,크기,차이를 정량화해야 해요.
* L1 Loss: 단순히 좌표 차이 (|x - x̂| + |y - ŷ| + |w - ŵ| + |h - ĥ|).
* GIoU Loss: IoU(intersection over Union)를 개선한 버전.
  * IoU: 예측 박스와 정답 박스 겹친 정도
  * GIoU: 겹침이 없을 때도 거리 기반 패널티 추가 -> 학습 안정
최종 박스 손실: $L_{box} = \lambda_{L1} \cdot L1 + \lambda_{giou} \cdot GIoU$

# 3. Hungarian Algorithm (헝가리안 알고리즘)
* Optimal Matching을 실제로 풀기 위한 고전 알고리즘
* "할당 문제(Assignment Problem)"를 효율적으로 풀어주는 알고리즘이에요
* DETR에서는
  * 예측 100개 x GT 7개 = 700개의 비용(cost) 행렬을 만든 뒤,
  * Hungarian Algorithm으로 "전체 비용 최소"가 되게 매칭합니다.
*즉, 어떤 Query사 어떤 GT를 담당할지 수학적으로 최적으로 정해줍니다.

# 4. Hungarian Loss (헝가리안 손실)
* "Hungarian Matching으로 정해진 짝"을 기준으로 Loss를 계산하는 걸 말합니다.
* 즉,
  * Query #23 ↔ 자동차 GT
  * Query #58 ↔ 사람 GT
  * Qyery #91 ↔ 배경
* 이렇게 매칭이 결정되면,
  * Query #23 -> 자동차 클래스 손실 + 박스 손실
  * Query #58 -> 사람 클래스 손실 + 박스 손실
  * Query #91 -> 배경 클래스 손실
* 나머지는 배경(∅) 클래스로 처리
* 공식적으로는:
* $L_{\mathrm{Hungarian}}=\sum_{(i,j)\in\sigma}\big[L_{\mathrm{cls}}(y_i,\hat{y}_j)+L_{\mathrm{box}}(b_i,\hat{b}_j)\big]$
* 여기서 σ는 Hungarian Algorithm으로 찾은 최적 매칭.

# 한 줄 요약
* Optimal Matching = "예측 박스와 GT를 최적으로 짝짓기"
* Bounding Box Loss = "좌표 차이(L1) + IoU(GIoU)"
* Hungarian Algorithm = "최적 매칭을 찾는 알고리즘"
* Hungarian Loss = "그 매칭 결과를 기준으로 계산한 총 손실"






































