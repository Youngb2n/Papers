 MobileNets: Efficient Convolutional Neural Networks for Mobile Vision Applications
 =================================================================================
 논문링크: <https://arxiv.org/pdf/1704.04861.pdf>   
 
 Introduction
 ----------------
 > VGG16 Model Archtecture    
     
  <img src="/paper_review/mobilenetv1/image/1.png" width="80%" height="80%" title="img1" alt="img1"></img>    
 
기존 CNN Model의 Archtecture을 확인해보면 단순히 채널 수를 증가시키며 convolution-pooling 의 연속으로 구성되어있다.
가장 직관적인 구조로 이해도 쉽고 구현하기도 쉬우나 모바일 환경에서 구동시키기엔 convolution 구조가 다소 무겁기 떄문에 파라미터량을 획기적으로 줄이기 위해
MobileNet이라는 Model 구조를 만들었다.


  Depthwise separable convolution
 -------------------------------
 > Convolution Block 비교   
    
 <img src="/paper_review/mobilenetv1/image/2.JPG" width="40%" height="40%" title="img1" alt="img1"></img>    
 > Depthwise separable convolution    
    
 <img src="/paper_review/mobilenetv1/image/7.JPG" width="80%" height="80%" title="img1" alt="img1"></img>    

Depthwise separable convolution이란 depthwise convolution과 pointwise convolution의 결합으로 보시면 된다.
위의 그림에 (b)에 해당하는 Depthwise Convolution은 채널별로 분리하여 각 채널을 각각의 커널로 convolution 하는 것이다.
이때, 입력의 채널과 출력의 채널은 항상 같다.
위의 그림 (c)에 해당하는 Pointwise convolution은 출력의 채널의 깊이 바꿀 수 있으며, 1x1 conv 연산하는 것을 말한다.

#### 파라미터 수 비교
> (a) standard convolution   
   
 <img src="/paper_review/mobilenetv1/image/3.jpg" width="40%" height="40%" title="img1" alt="img1"></img>    
 
 standard convolutional layer는 DF x DF x M 피쳐 맵 F를 입력하여 DF x N 피쳐 맵 G를 생성한다.
standard convolutional 계층은 DK x DK x M x N 크기의 convolution kernel K에 의해 파라미터화되며
DK는 제곱으로 가정된 커널의 공간적 차원이고 M은 입력 채널의 수, N은 출력 채널의 수를 말한다.

> (b) Depthwise convolution   
   
 <img src="/paper_review/mobilenetv1/image/4.jpg" width="40%" height="40%" title="img1" alt="img1"></img>    
배치 표준 및 ReLU 비선형 함수를 사용하며 입력 채널당 하나의 필터(입력 깊이)를 갖는 깊이 변환은 그림 위의 식과 같다. 
K^은 크기 DK x DK x M의 깊이 있는 Convolutional kernel로 filter가 channel에 적용되어 여과된 출력 피쳐 맵의 channel에 들어간다.

> (b)+(c) Depthwise separable convolution   
   
 <img src="/paper_review/mobilenetv1/image/5.JPG" width="40%" height="40%" title="img1" alt="img1"></img>   
 
> Result

 <img src="/paper_review/mobilenetv1/image/6.JPG" width="40%" height="40%" title="img1" alt="img1"></img>   


3 x 3 depthwise separable convolution을 사용하여 standard convolution보다 8~9배 더 적은 연산을 사용하는 것을 확인 할 수 있었습니다.

Model Archtecture
-----------------
> block layer 비교   
   
<img src="/paper_review/mobilenetv1/image/8.JPG" width="40%" height="40%" title="img1" alt="img1"></img>   

그림의 왼쪽은 standard convolutional layer 오른쪽은 depthwise separable convolutions를 보여줌.   

> MobilenetV1 Archtecture   
   
<img src="/paper_review/mobilenetv1/image/9.JPG" width="60%" height="60%" title="img1" alt="img1"></img>  

Result
----------
<img src="/paper_review/mobilenetv1/image/10.JPG" width="40%" height="40%" title="img1" alt="img1"></img>  

ImageNet DataSet에서의 실험결과 획기적으로 파라미터량이 줄었음에도 불구하고,
VGG16과 비교하였을 때 성능은 크게 떨어지지 않는 실험 결과를 보인다. 또한 smaller Mobilenet과 기존 파라미터 수가 적은 모델들과 비교실험결과
더 좋은 성능을 보이는것을 확인 할 수 있다.
