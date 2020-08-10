## Convolutinal Block Attention Module

### Abstract
Convolutional Block Attention Module(CBAM)은 CNN을 위한 간단하지만 효과적인 Attention Module이다. 중간 feature map을 고려할 때, 채널과 공간이라는 두 개의 별도 치수를 따라 관심 맵을 순차적으로 주입한 다음, Attention map을 입력 feature map에 곱하여 적응형 특징 정교함을 제공.

다양한 데이터셋에 적용하여 검증했으며, 분류 및 탐지 성능의 일관성 있는 개선을 보여줌.

###### Keywords: Object recognition, attention mechanism, gated convolution

### 1 Introduction
CNN은 지난 네트워크의 요소인 Depth, Width, Cardinality 부분을 중심으로 발전과 동시에 큰 성과를 보여줌. 그리고 Cardinality는 전체 매개변수 수를 저장할 뿐만 아니라 다른 두요소인 Depth, Width보다 강한 표현력을 가져오는 것을 Xception ResNext 네트워크의 경험을 통해서 확인하였음.

이러한 요소와는 별도로, 아키텍처 설계의 다른 측면인 Attention을 조사함.

이 논문의 Attention은 중요한 특징에 초점을 맞추고 불필요한 것을 억제하는 메커니즘을 사용하여 표현력을 높이는 것이다.

Convolution 운영은 채널과 공간 정보를 함께 혼합하여 유용한 기능을 추출한다. 그러므로 여기서는 두 가지 주요 차원을 따라 순차적으로 의미 있는 기능을 강조한다. 공간 축에 'what'과 'where'을 학습함으로써 어떤 정보를 강조하거나 억제할 것인가를 학습 시킬 수 있다.

#### Contribution. 

    - 간단하면서도 효과적인 Attention Module(CBAM)을 제안함.(CNN의 표현력을 높임)
    - 광범위하고 적절한 연구를 통해 Attention Module의 효과를 검증함.
    - 가벼운 Module을 연결하여 여러 데이터셋에서 다양한 네트워크의 성능이 크게 향상되었음을 확인함.

<p align="center"><img src="https://user-images.githubusercontent.com/45933225/75627787-c60d0780-5c16-11ea-8db6-329e879b8ca0.png" width="70%"></p>

위 그림은 채널과 공간 모듈로 CBAM으 전반적인 흐름을 보여줌.

### 2 Related Work

#### Network engineering.
설계가 좋은 네트워크가 다양한 애플리케이션에서 성능 향상을 보장함.

대규모 CNN의 성공적인 구현 이후 광범위한 Depth, Width, Cardinality 중심으로 한 여러 아키텍처가 제안되었으며 다른 측면인 'Attention'에 초점을 맞추고 있다.

#### Attention mechanism.
Squeeze-Excitation 모듈에서는 채널에 대한 Attention을 계산하기 위해 Global Average Pooling 기능을 사용하며 미세한 채널의 Attention을 추론하기 위해 최적의 기능이라는 것을 보여주며, 여기서는 추가적으로 Max pooling 기능도 사용할 것을 제안함.

앞에서 제시한 방법은 초점을 맞출 'where' 부분을 결정하는데 중요한 역할을 하는 공간적 관심을 놓친다. CBAM에서는 효율적인 구조에 기초하여 공간, 채널 Attention을 모두 이용하여 채널을 이용만 하는 것 보다 우월하다는 것을 입증함.

### 3 Convolutional Block Attention Module
중간 feature map F ∈ R^C×H×W 입력 받을 때 1D Channel attention map Mc ∈ R^C×1×1, 2D Spatial attention map Ms ∈ R^1×H×W 순차적으로 입력 받음.

<p align="center"><img src="https://user-images.githubusercontent.com/45933225/76643327-2d707300-6598-11ea-8196-2554624c5fb2.png" width="50%"></p>

 전체적인 과정은 다음과 같이 요약하며 F" 최종 출력을 말함.

### Channel, Spatial attention module.
 
<p align="center"><img src="https://user-images.githubusercontent.com/45933225/76643341-32cdbd80-6598-11ea-925d-c9aa4b282162.png" width="70%"></p>

#### Channel attention module.
Feature map에서 각 채널이 형상 검출기로 간주되기 때문에 Channel attention은 입력 이미지에 주어진 'what' 집중됨. 일반적으로 지금까지는 공간 차원을 압축하여 정보를 집계하는 Avg pooling을 사용하였으며 여기서는 채널에 대한 더 세밀한 관심을 추론하기 위해 Max pooling을 동시에 사용한다. 이것을 독립적으로 사용하는 것보다 모두 이용하는 것이 네트워크의 표현력을 크게 향상시키는 것을 확인하였음.

위 그림을 확인하면 Fc avg, Fc max 두개의 Full feature을 사용함으로써 Channel attention map Mc ∈ R^c×1×1을 생성함. 그리고 공유 네트워크로 1개의 숨겨진 레이어가 있는 다중 레이어 인식자(MLP)로 구성되며, 파라미터 오버헤드를 줄이기 위해 숨겨진 활성화 크기 R^c/r×1×1로 설정되며, 여기서 r은 감소 비율을 나타냄. 공유 네트워크는 각 설명자에게 적용된 후, 요소별 합계를 사용하여 출력 feature vector를 병합함.

<p align="center"><img src="https://user-images.githubusercontent.com/45933225/76643346-35c8ae00-6598-11ea-82ac-73d2bd67c351.png" width="50%"></p>

Channel attention은 다음과 같이 계산함. σ는 sigmoid 함수, W0 ∈ R^c/r×c, and W1 ∈ R^c×c/r,  MLP 가중치 W0, W1이 두 입력에 대해 공유되고 ReLU 활성화 기능이 W0에 따라 수행되는지 여부를 나타냄. 

#### Spatial attention module..
특징의 공간적 관계를 이용하여 Spatial Attention map을 생성함.

Spatial attention은 'where' 집중하는 것으로, channel attention을 보완하는 정보적인 부분이다. 그래서 channel attention을 따라 Avg, Max pooling을 적용하고 이를 연계하여 효율적인 특징을 생성함.

<p align="center"><img src="https://user-images.githubusercontent.com/45933225/76643350-395c3500-6598-11ea-8721-257c41f24a3e.png" width="50%"></p>

Convolution layer 적용하여 Spatial attention map Ms(F)를 생성하며, 두 개의 풀링을 사용하여 feature map의 채널 정보를 집계한 후 강조하거나 억제할 위치를 R^h×w  인코딩 함.

여기서  σ는 sigmoid 함수, f^7x7은 필터 크기가 7 x 7 인 convolution 연산이다.

#### Arrangement of attention modules.
입력 이미지를 고려하여 channel, spatial 두 개의 attention module이 'what', 'where'을 중심으로 상호 보완적인 계산을 함. 이에 따라 두 모듈을 병렬 또는 순차적으로 배치할 수 있음.

순차적인 배치가 평행한 배열보다 더 나은 결과를 보여주며 채널 우선 순위보다 공간 우선 순위가 더 나은 결과를 보여줌.

### 4 Experiments
표준 벤치마크에서 CBAM을 평가하며 이미지 분류와 개체 감지를 위한 여러 데이터셋을 활용하고 여러 아키텍처를 사용하여 CBAM의 일반적인 적용 가능성을 입증함.

<p align="center"><img src="https://user-images.githubusercontent.com/45933225/76643364-3d885280-6598-11ea-9cfe-3a54527f5fd0.png" width="70%"></p>

다음 그림은 ResNet의 ResBlock과 통합된 CBAM을 보여줌.

#### 4.1 Ablation studies
본 연구에서 데이터셋으로는 ImageNet-1k 사용하며 기본 아키텍처로 ResNet-50을 사용함.

Train images 120만개, 1,000개의 객체 클래스를 가진 test images 5만개로 구성되어 있음.

모듈 설계 과정은 세 부분으로 구성됨.

    - channel attention 계산에 대한 효과적인 접근법
    - Spatial attention을 추론함.
    - Channel, Spatial attention module 결합하는 방법을 고려함.

이 부분을 아래에 이어서 설명함.

#### Channel attention.
채널 관심의 3가지 변형, Avg-pooling, Max-pooling, 두 가지 공동 사용을 비교함. 여기서 두 가지를 공동 사용 하였을 때, 두 가지 동일한 의미 내장 공간에 있기 때문에 매개변수를 저장하기 위해 공유 MLP를 사용함.

<p align="center"><img src="https://user-images.githubusercontent.com/45933225/76643384-437e3380-6598-11ea-9f08-799c20be8f5b.png" width="50%"></p>
<p align="center"><img src="https://user-images.githubusercontent.com/45933225/76643387-46792400-6598-11ea-892a-525ef3ca04ad.png" width="50%"></p>
<p align="center"><img src="https://user-images.githubusercontent.com/45933225/76643401-4a0cab00-6598-11ea-83c7-550f76c9b332.png" width="50%"></p>

여기서 보이는 실험은 Channel attention module 사용하며 감소율은 16으로 수정함.

따라서 max, avg pooling의 각 고유한 톡징을 각 동시에 사용하고 특징에 공유 네트워크를 적용할 것을 제안함.

#### Spatial attention.
채널을 기반으로 Spatial attention의 효과적인 방법을 탐구함.

2D Spatial attention map을 생성하기 위해 모든 공간 위치에 걸쳐 각 픽셀의 채널을 정보를 인코딩 하여 하나의 컨볼루션 레이어를 적용함. 최종 attention map은 

sigmoid function에 의해 표준화 됨.

따라서 위의 결과를 바탕으로 채널, 공간 attention을 동시에 사용하고 kernel_size을 7 x 7을 사용함으로써 높은 성과를 얻을 수 있었음.

#### Arrangement of the channel and spatial attention.
이 실험에서는 순차 채널 공간, 순차 공간 채널, 두 개의 attention module의 병렬 사용 방법을 비교함.

결과적으로 채널과 공간을 사용을 동시에 사용함으로써 더욱 더 높은 성과를 이루었음.

### 4.2 Image classification on ImageNet-1K
CBAM이 대규모 데이터 셋의 다양한 아키텍처의 잘 일반회될 수 있음을 입증하며, 정확도 측면에서도 개선을 보여줌.

<p align="center"><img src="https://user-images.githubusercontent.com/45933225/76643411-4ed15f00-6598-11ea-98e9-fa8bb89bbbfc.png" width="50%"></p>

위의 결과를 보면 channel attention을 효과적으로 보완하는 new pooling의 효능울 보여줌.

그리고 전체 오버헤드와 파라미터와 연산 면에서 상당히 작다는 것을 발견함.

결과적으로 정확도를 크게 높일 뿐만 아니라 SE의 성능을 개선하며 높은 효율성을 보여줌.

<p align="center"><img src="https://user-images.githubusercontent.com/45933225/76643419-5264e600-6598-11ea-963c-f0db4259cbd1.png" width="50%"></p>

마지막으로 MobileNet에 동기 부여를 줌.

### 4.3 Network Visualization with Grad-CAM
Test data images 사용하여 Grad-CAM을 여러 아키텍처에 적용함.

결과적으로 컨볼루션 층에서 공간 위치의 중요성을 계산하여 고유한 클래스에 대한 관심 영역을 명확히 보여줌.

<p align="center"><img src="https://user-images.githubusercontent.com/45933225/76643424-555fd680-6598-11ea-93ef-164d1b8ef487.png" width="50%"></p>

CBAM 통합 네트워크의 결과 다른 방법보다 대상 객체 영역을 더 잘 커버하며 대상 객체 영역의 정보를 유용하게 이용하며 학습하고 특징을 모음. 그에 따라 클래스 점수도 증가한다는 것을 유의함.

### 4.4 Quantitative evaluation of improved interpretability
Grad-CAM 시각화에 기초한 사용자 연구를 수행 함. ImageNet 유효성 검사 세트에서 정확하게 분류된 50개의 이미지를 무작위로 선택 함.

시각화의 경우 Grad-CAM 값이 0.6 이상인 영상의 영역이 표시함. 그리고 CBAM이 기준치를 초과하여 향상된 해석을 보여주는 것을 분명히 볼 수 있음.

<p align="center"><img src="https://user-images.githubusercontent.com/45933225/76643436-5a248a80-6598-11ea-90d3-886d14a149bb.png" width="50%"></p>

### 4.5 MS COCO Object Detection
COCO 데이터셋을 기반으로 한 여러 아키텍처를 사용함으로써 성능 향상에 관심이 있었으며, 이는 CBAM에 의해 주어지는 향상된 표현력에만 기인할 수 있었다. 따라서 일반화 성능을 입증함.

### 4.6 VOC 2007 Object Detection
PASCAL VOC 2007 테스트 세트에 대한 실험을 추가로 수행 함.

<p align="center"><img src="https://user-images.githubusercontent.com/45933225/76643443-5ee93e80-6598-11ea-9b27-e4823f1965ee.png" width="50%"></p>
<p align="center"><img src="https://user-images.githubusercontent.com/45933225/76643444-614b9880-6598-11ea-910c-36a4500ef2e2.png" width="50%"></p>

위의 실혐 결과로 CBAM을 사용함으로써 더 높은 정확성을 달성할 수 있으며 동시에 매개변수 오버헤드를 동반한다는 점을 유의하고자 함.

따라서 CNN 표현력을 증가시키고자 CBAM을 제안 하였으며 채널과 공간이라는 두 가지 독특한 모듈로 Attention 기반 기능 개선을 적용하고 오버헤드를 작게 유지하면서 상당한 성능 향상을 달성함.

최종 결과적으로 강조하거나 억제하고 복구해야 할 대상과 위치를 학습함으로 그 성능을 입증하기 위해 다양한 실험을 실시 하였으며 CBAM이 세 가지 서로 다른 벤치마크 데이터셋에서 모든 기준선을 능가한다는 것을 관찰함.

