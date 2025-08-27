## 1. 일반 객체 탐지 (Class-Specific Detection)
보통 우리가 아는 YOLO, Faster R-CNN, DETR 같은 탐지기는 
* "이 박스 안에 물체가 있다" 뿐만 아니라
* "그 물체가 고양이냐, 자동차냐, 사람인가?" 까지 클래스 분류를 합니다.
즉, 일반 탐지 모델은
* Localization (위치) + Classification (클래스) 를 동시에 수행합니다.

## 2. Class-Agnostic Object Detection 개념
* Class-Agnostic = 클래스에 구애받지 않는다
* 따라서 모델은 "무언가 물체인지 아닌지" 만 판단합니다.
* 즉, 모델은 객체를 잡아내지만 그게 고양이인지, 자동차인지 구분하지 않음
* 쉽게 말하면:
* 일반 탐지: "여기 고양이 있음"
* 클래스 무관 탐지: "여기 물체 있음"

## 3. 왜 필요할까?
1. 회귀 클래스 / 미지의 클래스 탐지
   * 훈련 데이터에 없는 새로운 객체를 만나도 "이건 물체다"라고 탐지할 수 있음
   * 예: 자율주행에서 처음 보는 장애물이 있어도 인식 가능해야 함
2. Few-Shot / Zero-Shot 학습과 연결
   * 클래스 분류 대신 "물체성(objectness)"만 학습하면, 더 적은 데이터로도 학습 가능
3. 다단계 파이프라인
   * 먼저 Class-Agnostic Detector로 "모든 물체 후보"를 찾고 ->
   * 이후 별도의 분류기(Classifier)로 세부 클래스를 분류하는 방식 가능

## 4. 학습 방식
* 클래스별로 나누지 않고, 모든 객체를 **하나의 공통 클래스("object")** 로 취급
* Loss는 보통:
  * Objectness Score (물체인지 아닌지)
  * Bounding Box Loss (위치)
* -> "클래스 분류" 대신 단순히 "Object vs Background" 학습

## 한 줄 요약
Class-Agnostic Object Detection은
* "물체가 무엇인지는 몰라도, 물체 자체는 잡아내는" 탐지 방식으로,
* 새로운 클래스나 미지의 객체를 다뤄야 하는 상황에서 특히 유용합니다.

# 1. Baseline Models (초기 객체 탐지기)
* Faster R-CNN, R-CNN 계열이 대표적인 baseline
* 공통된 구조:
  1. 이미지에서 Region Proposal(물체 후보 영역) 생성
  2. 각 영역을 분류기 + 박스 회귀기에 넣어서 최종 탐지
* 즉, **물체가 있을 법한 후보 -> 분류/박스 예측** 의 파이프라인 구조

# 2. Region Proposal Network (RPN, 후보 영역 네트워크)
* Faster R-CNN에서 핵심 아이디어
* Anchor Box 기반 CNN으로 이미지 전체를 훑으면서
  * "여기에 물체 있을 것 같다(Objectness Score)"
  * "박스 위치(좌표)" 를 동시에 예측
* 결과: 수천 개 후보 박스를 만들어냄
* RPN은 클래스 정보는 고려하지 않고, "물체 vs 배경"만 판단 (Class-Agnostic Proposal)

# 3. Class-Aware Detector (클래스 인식 탐지기)
* RPN이 낸 후보 박스를 Class-Aware Detector에 전달
* Detector는 각 박스를 보고:
  * "이건 고양이", "이건 자동차", "이건 사람" ... 처럼 클래스별 확률 출력
  * 동시에 박스 위치를 세밀하게 다시 보정(Refinement)
* 즉, 여기서부터는 Class-Specific Detection

# 4. Fine-tuned Object-or-Not Binary Classification (후처리 단계)
* 어떤 연구/파이프라인에서는 Detector 결과를 다시 걸러내기 위해,
  * "이게 정말 물체가 맞나?" 하는 Object vs Not-Object 이진 분류기를 붙이기도 합니다.
* 특히 Class-Agnostic Detection 연구에서는 이 단계가 중요:
  * 모든 클래스의 GT를 합쳐서 "Object=1 / Background=0"만 학습
  * 즉, "고양이인지, 자동차인지는 몰라도 -> 물체냐 아니냐만 구분"
 
# 5. 최종: Object or Not Binary Classification
* 파이프라인 요약:
  1. RPN: 물체일 법한 후보를 뽑음 (Class-Agnostic)
  2. Detector: Class-Aware 모델이 클래스까지 예측
  3. Optional Fine-tuning: 다시 Object-or-Not 이진 분류로 필터링
* 이 구조 덕분에 -> Class-Agnostic Detector 연구에서는 "새로운 클래스"에도 대응 가능

# Adversarial Learning Framework가 적용된 Class-Agnostic Object Detection

## 1. 문제 상황
* 일반적인 Detector는 "고양이 / 자동차 / 사람" 같은 클래스 라벨을 이용해 학습 -> 풍부한 지도(supervision)
* 하지만 Class-Agnostic에서는 모든 객체를 하나의 클래스("object")로만 학습
* -> 클래스별 차별화된 피드백이 없으므로, 모델이 물체 vs 배경 구분에만 집중하고 일반화 능력이 부족해 질 수 있음

## 2. Adversarial Learning 적용 아이디어
Adversarial Learning은 여기서 **객체성(Objectness)** 을 강화하는 도구로 사용돼요.
1. Generator 역할 (Detector)
   * 이미지에서 후보 박스(Region Proposal) 또는 Object Query를 뽑음
   * 이 후보들이 "물체처럼" 보이도록 학습
2. Discriminator 역할
   * 입력된 박스/특징이 **진짜 물체(ground-truth box)** 인지, 아니면 Detector가 낸 **가짜 물체(배경 or 틀린 박스)** 인지 구분.
3. Adversarial Training
   * Detector는 Discriminator를 속여서 "내 박스도 물체다" 라고 설득하려 하고,
   * Discriminator는 진짜 물체와 가짜 물체를 구분하려 함
   * -> 이 과정에서 Detector가 점점 더 진짜 같은 **"objectness" 표현** 을 배우게 됨

## 3. 구체적 활용 예시
* Domain Adaptation 기반 Class-Agnostic Detection
  * 소스 도메인(예: COCO)과 타깃 도메인(예: 의료 영상)이 다를 때,
  * Discriminator가 "소스 vs 타깃"을 구분하도록 두고, Detector는 속이면서 도메인 불변 Objectness를 학습
* Weakly Supervised / Few-Shot Detection
  * 클래스 레이블이 부족할 때, Adversarial Loss를 넣어서 "이건 물체다" vs "배경이다" 기준을 강화
* Open-World / Novel Class Detection
  * 훈련 데이터에 없는 새로운 객체라도, Discriminator가 학습한 "물체성"을 바탕으로 탐지 가능









































































