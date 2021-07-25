# Faster R-CNN: Towards Real-Time Object Detection with Region Proposal Networks

[논문 링크](https://arxiv.org/abs/1506.01497)

## :bookmark:목차

0. Abstract
1. Introduction
2. Related Work
3. Faster R-CNN
4. Experiments
5. Conclusion

## 0. Abstract

Region Proposal Network(RPN) : fully convolutional network that simultaneously predicts object bounds and objectness scores at each position

2 step : 위치 찾기 + 분류

- Fast R-CNN은 selective search이용 (CPU)

>  RPN은 어떤 위치에 물체가 존재하는지 맞추기 위한 네트워크 
>
> -> 각각의 위치에 대해서 해당 물체의 좌표를맞추고 그 위치에 물체가 존재하는지 여부도 맞추게 된다. 
>
> 즉 어떠한 위치에 물제가 존재하는지만 판단하며 물체가 어떤 클래스인지는 x

## 1. Introduction

 Selective Search is an order of magnitude slower, at 2 seconds per image in a CPU implementation

RPN -> detection network와 feature map 자체를 sharing하므로 성능도 좋아지고 빨라짐

![image](https://user-images.githubusercontent.com/59350891/126364754-466621d7-28cf-4617-8792-9e45b1cd2d0c.png)

다양한 크기와 비율을 가지는 anchor box를 사용해서 속도 상에 이점을 얻음

- pyramid images 방식은 다양한 크기 이미지를 만들어서 각각 feature map 구해 사용한다.
- c는 anchor box 방식으로 다양한 box를 적용해서 예측 적용 -> 논문에서는 9개 사용

## 2. Related Work

![image](https://user-images.githubusercontent.com/59350891/126377254-a7ee9903-258a-4ea4-8ab1-5fba66a94f11.png)

**Faster R-CNN의 구조**

1. 하나의 이미지가 주어진다 
2. conv layers를 거치면서 feature maps를 뽑아낸다 
3. 이러한 feature maps이 공유되면서 RPN과 classifier network에 들어간다

## 3. Faster R-CNN

Faster R-CNN, is composed of two modules

- first module : RPN

- second module : Fast R-CNN detector

### 3.1 Region Proposal Networks

A Region Proposal Network (RPN) takes an image (of any size) as input and outputs a set of rectangular object proposals, each with an objectness score(사물 존재 여부).

feature map : ZFNet, VGG-16

![image](https://user-images.githubusercontent.com/59350891/126379539-a5e3dace-4679-49e9-b9a3-9f2902bec002.png)

1. 한장의 이미지를 conv layer에 넣어서 conv feature map을 뽑는다

2. 이러한 feature map에 대해 다양한 크기와 비율을 가지는 anchor box를 이용해서 슬라이딩을 하면서 각 위치에 대해 prediction 진행하여 256차원으로 mapping 수행  (기본 크기는 3*3 anchor box)

3. feature을 토대로 regression layer와 classification layer을 거친다.

### 3.2 Sharing Features for RPN and Fast R-CNN

 ways for training networks with features shared

1. Alternating training (번갈아가면서 학습) -> 논문에서 선택한 방법

- RPN -> Fast R-CNN -> RPN -> Fast R-CNN

**4-Step Alternating Training**

1. end to end 방법으로 RPN network만 학습을 진행 
2. RPN network에서 만들어낸 proposal을 이용해서 Fast R-CNN 학습 (여기까지는 conv layer가 온전히 공유x)
3. RPN network에 포함되지 않는 conv layer는 고정한 상태로 RPN에 포함되어 있는 추가적인 conv layer에 대해서만 학습 진행
4. 마찬가지로 conv layer는 고정한 상태에서 Fast R-CNN에 대해서만 포함되어 있는 layer에 대해서만 학습 진행

-> 즉 conv layer가 고정되어 있고 RPN과 Fast R-CNN 각각 포함되어 있는 layer만 학습 진행

=> 이렇게 함으로써 conv layer는 RPN과 Fast R-CNN이 완전히 공유

### 3.3 Implementation Details

For anchors, we use 3 scales with box areas of 128^2 , 256^2 , and 512^2 pixels, and 3 aspect ratios of 1:1, 1:2, and 2:1. -> 총 9개

<img src="https://user-images.githubusercontent.com/59350891/126663887-cc6fec25-7cec-4010-b838-1353e8189a07.png" alt="image" style="zoom: 50%;" />

The anchor boxes that cross image boundaries need to be handled with care

-> During training, we ignore all cross-boundary anchors so they do not contribute to the loss.

-> 일반적으로 2만개 anchor 존재시  학습시 cross boundaries anchor을 제거함으로써 6000개만 남겨서 학습에 사용

## 4. Experiments

SS : selective search / EB : edge box

![image](https://user-images.githubusercontent.com/59350891/126672764-7827960d-2a91-4dd5-93f1-44efbdd413ee.png)

## 5. Conclusion

We have presented RPNs for efficient and accurate region proposal generation
