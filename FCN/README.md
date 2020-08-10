Fully Convolutional Networks for Semantic Segmentation
=======================================================
논문 링크 : <https://arxiv.org/pdf/1411.4038.pdf>

Fully Convolutional Model
-------------------------
> Transforming fully connected layers into convolution layers   

<img src="/FCN/image/1.JPG" width="80%" height="80%" title="img1" alt="img1"></img>    

FCN이 주목한 부분은 Classfication에서 성능을 검증받은 좋은 네트워크(AlexNet, VGGNet, GoogleNet) 등을 이용하는 것이다.
이들 대부분의 Classification을 위한 네트워크 뒷단에 분류를 위한 fully connected layer가 오는데,
이 레이어가 고정된 크기의 입력만을 받아들이는 문제가 있다. 또 한가지 결정적인 문제는 fully connected layer을 통과하면 위치정보가
사라지게 되는 것이다. Segmentation에 이용하려면 위치 정보를 알 수 있어야 하는데 불가능 하기에 심각한 문제가 된다.   
그래서 FCN 개발자들은 fully connected layer를 1x1 Convolution layer로 볼 수있다는 점을 주목하였고 위치정보를 가질 수 있게 된다.
또한 이제는 모든 network 구조가 Convolutional network로 구성이 되기 떄문에 더 이상 입력 이미지 크기에 제한을 받지 않게 된다.

UpSampling
----------
> Upsampling 과정   
   
<img src="/FCN/image/2.JPG" width="60%" height="60%" title="img1" alt="img1"></img>     

>DeConvolution 방식    
    
<img src="/FCN/image/3.JPG" width="60%" height="60%" title="img1" alt="img1"></img>    

여러 단계의 Convolution + pooling 단계를 거치게 되면, feaeture-map 의 크기 줄어 들게 된다.
픽셀 단위로 예측을 하기 위하여 줄어든 feature-map을 다시 키우는 과정을 거쳐야 한다.
1x1 Convolution을 거치면서 얻어진 score 값을 원 영상의 크기로 확대를 하려면 Bilinear interpolation 방식을
사용 하면 간단하다. 하지만 end-to-end 학습의 관점에서는 고정된 값을 사용하는 것이 아니라 학습을 통해 결정이 되는 것이 좋기 때문에
Backward Convolution(DeConvolution)을 사용한다. 이렇게 되면 경우에 따라서 Blinear 한 필터를 학습 할 수도 있고
non_linear upsampling 또한 가능 하게 된다.   

Skip Architecture
----------------
이 처럼, Interpolation을 통해 coarse map에서 dense map을 도출할 수 있었지만 근본적으로
feature map의 크기가 너무 작기 때문에 예측된 dense map의 정보는 여전히 거칠수 밖에 없다.
무려 Stride가 32인 Up-sampling 과정에서 얼마나 많은 정보가 손실될 지를 생각해보면 당연한 결과이다.

> FCN-32 결과

<img src="/FCN/image/4.JPG" width="80%" height="80%" title="img1" alt="img1"></img>    



본 논문에서는 정확하고 상세한 구분(Segmentation)을 얻기 위해 Deep & Coarse(추상적인) 레이어의 의미적(Semantic) 정보와
Shallow & fine 층의 외관적(appearance) 정보를 결합한 Skip architecture를 정의한다. 

   
<img src="/FCN/image/6.JPG" width="80%" height="80%" title="img1" alt="img1"></img>
이러한 직관은 Visualizing and Understanding Convolutional Networks 연구를 통해서도 엿볼 수 있다. 
시각화 모델을 통해 입력 이미지에 대해 얕은 층에서는 주로 직선 및 곡선, 색상 등의 낮은 수준의 특징에 활성화되고,
깊은 층에서는 보다 복잡하고 포괄적인 개체 정보에 활성화된다는 것을 확인할 수 있다. 
또한 얕은 층에선 local feature를 깊은 층에선 global feature를 감지한다고 볼 수 있다.
FCNs 연구팀은 이러한 직관을 기반으로 앞에서 구한 Dense map에 얕은 층의 정보를 결합하는 방식으로 Segmentation의 품질을 개선하였다.

> FCN Architecture   
   
<img src="/FCN/image/5.JPG" width="80%" height="80%" title="img1" alt="img1"></img>    

FCN에서는 1/32 크기에서의 feature 만 이용하는게 아니라, 1/16, 1/8에서의 feature 값도 같이 사용하는 방식을 취하였다.


실험 결과
--------

>FCN-16s
   
<img src="/FCN/image/7.JPG" width="80%" height="80%" title="img1" alt="img1"></img>    

>FCN-8s

<img src="/FCN/image/8.JPG" width="80%" height="80%" title="img1" alt="img1"></img>    

>FCN VGG result   

<img src="/FCN/image/9.JPG" width="80%" height="80%" title="img1" alt="img1"></img>    

각 Pooling에 Prediction을 위해 추가된 Conv layer의 필터는 0으로,
Trainable Backwards convolution은 Bilinear interpolation으로 초기화한 후 학습을 진행하였다.
이러한 Skip Architecture를 통해 다음과 같이 개선된 Segmentation 결과를 얻을 수 있다.
실제 성능 지표에서도 FCN-32s > FCN-16s > FCN-8s 순으로 결과가 나아지는 것을 확인할 수 있다.
