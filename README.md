# Lane-Detection-Algorithms
Organizing and implementing multiple papers

## 1. Hough Transformation & RACSAC

## 2. Real time Detection of Lane Markers in Urban Streets
Inverse Perspective Mapping 을 사용하여 좀 더 정교한 Line Detection을 수행

## 3. A Novel Lane Detection System With Efficient Ground Truth Generation
Interpolation을 이용해 효과적으로 Annotation 하는 방법을 제시.

## 4. VPGNet


## 5. Light weight CNNs by Self attention distillation
기존의 딥러닝 기반 Lane Detection Algorithms들은 Semantic Segmentation task임.

또한, 위에 두 가지 1번과 2번 방법은 단순한 시나리오에만 적용이 가능하다는 문제점이 있음.

즉 Lane을 픽셀 별로 어노테이션하게 되는데 이는 이미지 상에서 상대적으로 적은 수의 픽셀을 차지하고 이를 해결하기 위해 Message Passing 방법과 Multi task learning 방법을 사용했음.

하지만, Multi task learning 같은 경우에는 소실점을 마킹하고 운전 구역을 Annotation 하는 등 더 비싼 데이터를 이용함. Message Passing은 Feed forward time이 상대적으로 증가하고 낮은 하드웨어 스펙을 요구하는 Lane Detection 특성 상 큰 문제점이 될 수 있음.

