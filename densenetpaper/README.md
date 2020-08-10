Densely Connected Convolutional Networks
=========================================
논문 링크 : <https://arxiv.org/pdf/1608.06993.pdf> 


Dense Connectivity
------------------

입력에 가까운 layer와 출력에 가까운 layer 사이에 shorter connection이 포함되면, 더 깊고 정확하면서 효율적으로 학습할 수 있다. 이 논문에서는 이러한 관찰을 받아들여, feed-forward 방식으로 각 layer를 다른 모든 layer에 연결한 DenseNet을 소개한다. 이전 layer들의 feature map을 계속해서 다음 layer의 입력과 연결하는 방식이며 이러한 방식은 ResNet에서도 사용이 되었습니다.하지만 ResNet은 feature map 끼리 add 를 해주는 방식이었다면 DenseNet은 feature map끼리 Concat을 시키는 것이 가장 큰 차이점입니다. 이러한 구조를 통해 얻을 수 있는 이점은 다음과 같습니다.
* Vanishing Gradient 개선
* Feature Propagation 강화
* Feature Reuse
* Parameter 수 절약

Growth Rate
-----------
<img src="/densenetpaper/image/1.JPG" width="80%" height="80%" title="img1" alt="img1"></img>    

각각 feature map끼리 densely 연결이 되는 구조이다 보니 자칫 feature map의 channel 개수가 많은 경우 계속해서 channel-wise로 concat이 되면서 channel이 많아 질 수 있습니다. 그래서 DenseNet에서는 각 layer의 feature map의 channel 개수를 굉장히 작은 값을 사용하며, 이 때 각 layer의 feature map의 channel 개수를 growth rate(k) 이라 부릅니다.  



BottleNeck Layer
----------------
<img src="/densenetpaper//image/2.JPG" width="80%" height="80%" title="img1" alt="img1"></img>    

ResNet 과 inception 등에서 사용되는 Bottleneck layer 아이디어도 DenseNet에서 사용하였다. 1x1 Convolution layer에서 dimension을 줄여 Feature map의 갯수를 줄였다가 그 뒤 growth rate 만큼의 feature map을 생성하여 computationnal Complexity를 줄일 수 있다. 구조 1x1 Convolution layer을 통해 4* grow rate 만큼의 feature map을 생성하고 3x3 Convolution Layer을 통해 grow rate 개 만큼의 feature map으로 줄여준다. bottleneck layer을 통해 사용하지 않을 때 보다 더 좋은 성능을 보인다.

Transition Layer
----------------

Transition layer는 feature map의 width, height의 size를 줄여주고 feature map의 개수를 줄여주는 역할을 담당하고 있습니다. 마지막 Dense Block을 제외한 나머지 Dense Block 뒤에 연결이 되며 Batch Normalization, ReLU, 1x1 convolution, 2x2 average pooling 으로 구성이 되어있습니다. 1x1 Convolution Layer를 통해  feature map의 필터 수를 theta(0.5)로 줄여주며 average pooling을 통해 width와 height size 또한 절반으로 줄어든다.


Composite function
------------------
<img src="/densenetpaper//image/3.JPG" width="80%" height="80%" title="img1" alt="img1"></img>    

DenseNet은 ResNet의 구조에 대해 분석한 “Identity mappings in deep residual networks, 2016 ECCV” 논문에서 실험을 통해 제안한 BatchNorm-ReLU-Conv 순서의 pre-activation 구조를 사용하였습니다.

실험내용
--------
> ImageNet의 DenseNet Architecture   
   
<img src="/densenetpaper//image/4.JPG" width="80%" height="80%" title="img1" alt="img1"></img>    
   
>Model 별 파라미터 비교  
   
<img src="/densenetpaper//image/6.JPG" width="80%" height="80%" title="img1" alt="img1"></img>    

cifar &  SVHN Dataset의 error와 파라미터를 비교한 그림이다. k는 network의 grow rate, 최고의 성능은 파란색, "+"는 표준 데이터의 확대를 나타낸다. DenseNets의 모든 결과(C10, C100, SVHN)는 Dropout을 사용하여 얻는다. DenseNets는 ResNet보다 적은 파라미터를 사용하면서 더 낮은 오류율을 달성한다. 데이터 증가 없이 DenseNet은 큰 차이로 더 나은 성능을 발휘한다.

> ImageNet의 DenseNet과 Resnet top1 error 비교   
   
<img src="/densenetpaper//image/5.JPG" width="80%" height="80%" title="img1" alt="img1"></img>    
   

