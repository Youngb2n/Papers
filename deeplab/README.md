DeepLab: Semantic Image Segmentation with Deep Convolutional Nets
==============================================================================

   
> 논문링크   
    
DeepLabV1 : <https://arxiv.org/pdf/1802.02611.pdf>   
DeepLabV2 : <https://arxiv.org/pdf/1802.02611.pdf>    
DeepLabV3 : <https://arxiv.org/pdf/1802.02611.pdf>   
DeepLabV3+ : <https://arxiv.org/pdf/1802.02611.pdf>  

Introduction
------------

Semantic Segmentation을 해결하기 위한 방법론은 여러가지가 존재한다.
 그 중 DeepLab 시리즈는 여러 segmentation model 중 성능이 상위권에 많이 포진되어 있는 model들이다. 
 그 중에서도 가장 성능이 높으며 DeepLab 시리즈 중 가장 최근에 나온 DeepLab V3+에 대해 살펴보기 전에 전 버전들의 논문에 어떤 변화가 있었는지 살펴보겠습니다.
 전체적으로 DeepLab은 semantic segmentaion을 잘 해결하기 위한 방법으로 atrous convolution을 적극적으로 활용할 것을 제안하고 있다.
 
 - DeepLab V1 : Atrous convolution을 처음 적용

 - DeepLab V2 : multi-scale context를 적용하기 위한 Atrous Spatial Pyramid Pooling (ASPP) 기법을 제안

 - DeepLab V3 : 기존 ResNet 구조에 Atrous Convolution을 활용해 좀 더 Dense한 feature map을 얻는 방법을 제안

 - DeepLab V3+ : Depthwise Separable Convolution과 Atrous Convolution을 결합한 Atrous Separable Convolution의 활용을 제안
 
 
Atrous Convolution
------------------
<img src="/deeplab/image/1.JPG" width="80%" height="80%" title="img1" alt="img1"></img>    

Atrous에서 trous는 구멍(hole)을 의미함으로써, Atrous Convolution은 기존 Convolution과 다르게 필터 내부에 빈 공간을 둔 채 작동하게 된다.
위 그림에서 얼마나 빈 공간을 둘지 결정하는 파라미터값 r(rate의 약자)이 1인 경우, 기존 Convolution과 동일하고 r이 커질수록 빈 공간이 넓어지게 되는 것이다.
이러한 Atrous Convolution을 활용함으로써 얻을 수 있는 이점은 기존 convolution과 동일한 양의 파라미터와 계산량을 유지하면서도, field of view(한 픽셀이 볼 수 있는 영역)를 크게 가져갈 수 있게 된다는 것이다.
보통 Semantic Segmentation에서 높은 성능을 내기 위해서는 CNN의 마지막에 존재하는 한 픽셀이 입력값에서 어느 크기의 영역에서 커버할 수 있는지를 결정하는 receptive field 크기가 중요하게 작용한다.
즉, 여러 convolution과 pooling 과정에서 디테일한 정보가 줄어들고 특성이 점점 추상화되는 것을 어느정도 방지할 수 있기 때문에 DeepLab series에서는 이를 적극적으로 활용하려 노력한다.


Atrous Spatial Pyramid Pooling(ASPP)
------------------------------------
<img src="/deeplab/image/2.JPG" width="80%" height="80%" title="img1" alt="img1"></img>    

Semantic segmentaion의 성능을 높이기 위한 방법 중 하나로, spatial pyramid pooling 기법이 자주 활용되고 있는 추세이다.
 DeepLab V2에서 feature map으로부터 rate가 다른 Atrous Convolution을 병렬로 적용한 뒤,
 이를 다시 합쳐주는 ASPP기법을 활용할 것을 제안했었다. 
 최근 발표된 PSPNet에서도 Atrous Convolution을 활용하진 않았지만 이와 비슷한 Pyramid Pooling 기법을 활용하였다.
 이러한 이러한 방법들은 multi-scale context를 모델 구조로 구현하여 보다
 정확한 Semantic Segmentation을 수행할 수 있도록 도우며, DeepLab V3부터는 ASPP를 기본 모듈로 계속 사용하고 있다.
 
 Depthwise separable convolution
-------------------------------
<img src="/deeplab/image/5.JPG" width="80%" height="80%" title="img1" alt="img1"></img>    

Convolution 연산에서 Channel 축을 필터가 한 번에 연산하는 대신에 위 그림과 같이 입력 영상의 Channel 축을 모두 분리시킨 뒤,
Channel 축 길이를 항상 1로 가지는 여러 개의 Convolution 필터로 대체시킨 연산을 Depthwise Convolution이라고 한다.
이처럼 복잡한 연산을 수행하는 이유는 기존 Convolution과 유사한 성능을 보이면서도
사용하는 파라미터 수와 연산량을 획기적으로 줄일 수 있기 때문이다.

Encoder-Decoder
----------------
<img src="/deeplab/image/3.JPG" width="80%" height="80%" title="img1" alt="img1"></img>    

 마지막으로 DeepLab V3+는 위에서 설명한 모듈을 Encoder-Decoder로 구조화시켰다.
 U-Net과 유사하게 intermediate connection을 가지는 encoder-decoder 구조를 적용하여 보다 정교한 object boundary를 예측 할 수 있게 하였다.
 DeepLab V3+에서는 Encoder로 DeepLab V3를 사용하고, Decoder로 Bilinear Upsampling 대신 U-Net과 유사하게 Concat해주는 방법을 사용한다.
 
Modified Aligned Xception
--------------------------
<img src="/deeplab/image/6.JPG" width="80%" height="80%" title="img1" alt="img1"></img>    
eepLab V3+에서는 Xception을 backbone으로 사용하지만 MSRA의 Aligned Xception과 다른 3가지 변화를 주었다.   
빠른 연산과 메모리 효율을 위해 Entry Flow Structure 를 수정하지 않았으며 Atrous Separable Convolution을 적용하기 위해 모든 Pooling Opearation을 Depthwise Separable Convolution으로 대체하였다.
또한 각각의 3*3 Depthwise Convolution 이후에 추가적으로 Bath-norm과 ReLU 활성화 함수를 추가해주었다.



 
 Model Archtecture
 -----------------
 <img src="/deeplab/image/4.JPG" width="80%" height="80%" title="img1" alt="img1"></img>   
 
 실험내용
 -------
  <img src="/deeplab/image/7.JPG" width="80%" height="80%" title="img1" alt="img1"></img>   
   다양한 파라미터와 세팅에 대해서 실험을 진행하였는데, 우선 ResNet-101 구조를 Encoder로 사용하였을 때, 성능을 측정한 것이다.
   Decoder 부분을 bilinear upsampling 대신, 단순화된 U-Net 구조로 변경할 경우 기존 대비 mIOU 1.64% 향상이 있음을 확인 할 수 있다.

