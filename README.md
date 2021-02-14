# Lane-Detection-Algorithms
Organizing and implementing multiple papers

## 1. Hough Transformation & RANSAC

## 2. Real time Detection of Lane Markers in Urban Streets
Inverse Perspective Mapping 을 사용하여 좀 더 정교한 Line Detection을 수행하는 알고리즘이다.

다음과 같은 변환을 사용하는데

<img width="455" alt="스크린샷 2021-02-14 오후 3 53 14" src="https://user-images.githubusercontent.com/68293683/107870570-c0b6d480-6edc-11eb-83a2-21467fc68767.png">
<img width="399" alt="스크린샷 2021-02-14 오후 3 52 48" src="https://user-images.githubusercontent.com/68293683/107870558-b09ef500-6edc-11eb-8cec-4711c9c88420.png">

결과적으로

<img width="434" alt="스크린샷 2021-02-14 오후 3 53 00" src="https://user-images.githubusercontent.com/68293683/107870564-b7c60300-6edc-11eb-9c16-aee7d988e402.png">

이런 이미지로 변한다.

그리고, 영상처리에서 많이 쓰이듯 필터쓰고 Threshold 값 설정해서

<img width="456" alt="스크린샷 2021-02-14 오후 3 55 11" src="https://user-images.githubusercontent.com/68293683/107870586-083d6080-6edd-11eb-899c-261eef97d8a3.png">

이와 같은 이미지로 변하고 여기서 Hough Transform과 RANSAC을 수행한다.
## 3. A Novel Lane Detection System With Efficient Ground Truth Generation
Interpolation을 이용해 효과적으로 Annotation 하는 방법을 제시.

## 4. VPGNet
소실점과 차선 그리고 운전 구간 들을 어노테이션한 후 Multi Task Learning을 이용해 학습시킨 Network. SOTA 이긴하지만 MTL 이 대개 그렇듯 Loss들 간에 Balance를 맞추는 것이 어렵고 많은 양의 Annotation이 필요하다는 점은 단점이다. (또한, 높은 사양의 컴퓨팅 파워를 요구하기 때문에 현업에는 적합하지 않을 수 있다.)

<img width="324" alt="스크린샷 2021-02-14 오후 3 51 13" src="https://user-images.githubusercontent.com/68293683/107870527-79304880-6edc-11eb-823d-f894c3673bc3.png">

모델의 구조는 다음과 같고

<img width="969" alt="스크린샷 2021-02-14 오후 3 59 01" src="https://user-images.githubusercontent.com/68293683/107870648-90236a80-6edd-11eb-951b-52e8c83c6625.png">

Loss function은 다음과 같다.

<img width="353" alt="스크린샷 2021-02-14 오후 4 07 56" src="https://user-images.githubusercontent.com/68293683/107870784-cf05f000-6ede-11eb-8e9f-f2e3f50a2fdd.png">

이제 Vaninshing Point를 이용해 어떻게 Lane을 Regression 해줄 지가 중요한데,

우선 모델에서 Lane일 확률이 높다고 판정된 픽셀을 각각 bin에 넣으면서 클러스터링을 해준다.

이 때, bin의 맨 위에 인접한 픽셀이 있다면 해당 bin으로 들어가고 그렇지 않다면 새로운 bin을 만든다. 각각의 bin은 군집을 형성할 것이다.

이제 bin에서 VP로부터 가장 먼 점을 골라준다. 그 해당 점으로부터 가장 가까운 VP들로 연결된 bin의 집합들이 있을 것이다.

그 각각의 bin에서 VP로부터 가장 먼 점들을 polynomial regression 해서 Line을 그려준다.

## 5. Light weight CNNs by Self attention distillation
RANSAC을 이용한 방법에는 비가 왔을 때 오작동을 일으키는 경향이 있음. (직선을 찾아서 바꾸는 경우이기 때문에)

딥러닝의 경우 픽셀 별로 어노테이션하게 되는데 이는 이미지 상에서 상대적으로 적은 수의 픽셀을 차지하는 Lane의 경우 imbalanced한 데이터를 갖게 했음.
이를 해결하기 위해 Message Passing 방법과 Multi task learning 방법을 사용함.

하지만, Multi task learning 같은 경우에는 소실점을 마킹하고 운전 구역을 Annotation 하는 등 더 비싼 데이터를 이용함. 또한 MTL의 경우 Loss를 최적화하기 힘들게 설계되어있음. Message Passing은 Feed forward time이 상대적으로 증가하고 낮은 하드웨어 스펙을 요구하는 Lane Detection 특성 상 큰 문제점이 될 수 있음. 이러한 이유에서 저자는 Lightweight CNNs를 개발했다고 한다.

모델의 구조는 인코더-디코더 구조를 따르는 Enet 기반이며, Lane인지 아닌지 평가하기 위한 Binary Classification Output을 추가했다.

<img width="735" alt="스크린샷 2021-02-14 오후 8 23 26" src="https://user-images.githubusercontent.com/68293683/107875298-8102e380-6f02-11eb-88cd-5b179077bf21.png">


Loss function은 각 픽셀의 Segmentation의 Loss, 그리고 Lane pixel의 IOU Loss, Lane이 존재하는지 안하는지 표기하는 Binary Classification Loss, 그리고 Self Distillation Loss로 이루어진다.

<img width="353" alt="스크린샷 2021-02-14 오후 8 20 38" src="https://user-images.githubusercontent.com/68293683/107875245-1c478900-6f02-11eb-99d6-f7c3da8c512d.png">

Self Distillation Loss에 대해 알아보면, 우선 Attention Transfer에 대한 내용의 이해가 필요하다. (Attention Mechanisms 레포지토리에 해당 설명이 있음.)

모델을 절반 정도 학습한 시점에서 다음 레이어의 Attention Map에 대한 Activate Attention Transfer를 구해 그 차이를 Loss로 구하는 과정이다. 
그렇게 되면 이전의 Layer가 이후의 Layer에 좀 더 필요한 지점에 Attention 하게끔 학습이 된다는 것이다.

<img width="735" alt="스크린샷 2021-02-14 오후 8 25 03" src="https://user-images.githubusercontent.com/68293683/107875350-b9a2bd00-6f02-11eb-950b-0d53f5ccd8ec.png">

Post Processing 방법은 우선 9x9 크기의 Smoothing filter를 Probability Map에 적용한다.

그 이후 방법은 Probability 가 0.5 이상인 pixel 들에 대해 다음과 같은 과정을 진행한다.

가장 높은 Probability 를 갖는 Pixel에 대해 주변 20개의 row에 대해 가장 높은 Probability를 갖는 점들을 찾는다.

이후 Cubic Spline을 이용해 Lane을 Prediction 한다.

## 6. LaneATT

