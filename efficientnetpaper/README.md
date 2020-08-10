EfficientNet: Rethinking Model Scaling for Convolutional Neural Networks
========================================================================
논문 링크 : <https://arxiv.org/pdf/1905.11946.pdf>


Model Scaling
--------------
> Model Scaling 기법들   

<img src="/efficientnetpaper/image/1.JPG" width="80%" height="80%" title="img1" alt="img1"></img>    

Convolution Network의 크기(scale)을 키우는 방법은 3가지가 있다. layer의 갯수를 늘리는 depth scaling,
width(필터 수)를 늘리는 width scaling, input image의 해상도를 높이는 resolution scaling이 자주 이용된다.
기존 방식의 모델들은( Resnet : Depth scaling , moblienet : Width scaling 등등) 3가지 Scaling 기법들을 동시에 사용하는 경우가 없었으며
Scaling 기법에 어떤 기법이 효과적인지 또한 일일이 실험을 해봐야 결과가 나오는 어려움 또한 있었다.


Compound Scaling
--------------

그리하여 각 Scaling 기법마다 하나의 Scaling factor 이외 나머지 값은 고정을 하고 키워가며 정확도의 변화를 측정하였다.

> Single Demension Model Scaling 실험 결과   

<img src="/efficientnetpaper/image/2.JPG" width="80%" height="80%" title="img1" alt="img2"></img>   

그래프를 비교해 보면 width Scaling과 Depth Scaling은 비교적 이른 시점에 Accuracy가 포화 되는데 비해
Resolution Scaling은 키울소록 Accuracy가 잘오르는 것을 확인 할 수 있다.

> 3가지 Scaling vector의 따른 비교 실험 결과   

<img src="/efficientnetpaper/image/3.JPG" width="80%" height="80%" title="img1" alt="img3"></img>   

초록색 선과 노란색 선을 비교결과 depth 를 키우는것 보다 resolution을 키우는 것이 Accuracy 향상에 효과적임을 보인다.
또한 빨간색 선과 비교해보면 1 가지 or 2가지 scaling factor을 키우는것 보단 3가지 scaling factor를 동시에 키우는 것이
효과적임을 보이고 있다.
   
그리하여 이 논문에서는 모델을 고정하고 3가지를 조절하는 방법을 제안하고 있는데,
이때 고정하는 모델를 좋은 모델로 선정하는 것이 굉장히 중요한데 아무리 scaling factor를 조절해도,
초기 모델 자체의 성능이 낮다면 임계 성능도 낮기 때문입니다.
이 논문에서는 MnasNet과 거의 동일한 search space 하에서 AutoML을 통해 모델을 탐색하였고,
이 과정을 통해 찾은 작은 모델을 EfficientNet-B0 이라 부르고 있습니다.

> EfficientNet-B0 Model Architecture   
   
<img src="/efficientnetpaper/image/4.JPG" width="80%" height="80%" title="img1" alt="img4"></img>    
주요블록층은 Mobilenet v2, mnasnet, Squeeze-and-Excitation의 seblock 등이 사용 되었다.
    
> Compund Scaling
   
<img src="/efficientnetpaper/image/5.JPG" width="80%" height="80%" title="img1" alt="img5"></img>    

실험내용
-------

실험에서 depth(알파), width(베타), resolution(감마) 은 간단한 grid search 를 통해 구하는 방식을 제안하고 있으며,
처음 단계 에서는 파이를 1로 고정한 뒤, 타겟 데이터 셋에서 좋은 성능을 보이는 값들을 찾아낸다.
본 논문에서 알파 값은 1.2 베타 값은 1.1 감마 값은 1.15를 사용하고 있으며,
이 scaling factor 를 고정한 뒤 파이를 키워주며 모델의 사이즈를 키워준다.
기존 사람이 설계한 ConvNet, AutoML 을 통해 찾은 ConvNet 들과 비교한 결과는 다음과 같다.   

> ImageNet에 대한 실험결과   
   
<img src="/efficientnetpaper/image/6.JPG" width="80%" height="80%" title="img6" alt="img6"></img>   

> Activation Map   

<img src="/efficientnetpaper/image/7.JPG" width="80%" height="80%" title="img7" alt="img7"></img>   

위 결과는 이미지 분류 시 class activation map 의 결과인데, 3개의 scaling factor 를 
각각 고려할 때 보다 동시에 고려했을 때(compound scaling)가 더 정교한 CAM를 얻을 수 있음을 확인 할 수 있다. 

