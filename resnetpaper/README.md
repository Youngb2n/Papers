Deep Residual Learning for Image Recognition
============================================

논문 링크 : <https://arxiv.org/pdf/1512.03385.pdf>

Introduction
------------
VGGnet 이후 망이 깊어 질수록 성능이 올라간다고 하지만 layer가 일정 수 이상 많아져 인공신경망이 깊어지면 Gradient Vanishing/Exploding 문제로 학습 장애가 온다.   
> plane network 20-layer와 56-layer의 train error와 test error   

<img src="/paper_review/resnetpaper/image/1.JPG" width="80%" height="80%" title="img1" alt="img1"></img>   

그래서 더 깊은 레이어까지 잘 학습이 되도록하는 방법을 Kaming he가 shorcut(Skip) connection을 고안했습니다.   

Related work
------------
>Residual Block   
    
<img src="/paper_review/resnetpaper/image/2.JPG" width="80%" height="80%" title="img1" alt="img1"></img>   

ResNet의 핵심은 Residual Block이라고 생각한다. 그림 왼쪽 방식은 입력 x를 y에 매핑하는 H(x)를 얻는 것이 목적으로 하는 plain network의 direct mapping을 의미한다.     
하지만 그림 우측의 Residual Block은 입력 x와 H(x)의 차이를 0에 수렴하게 되어 입력과 출력이 같아지도록 하는 것을 목적으로 한다. F(x)를 입력 x와 출력 H(x)의 잔차(Residual)라고 가정하면 F(x)= H(x)-x 라고 표현할 수 있다. 식을 재정의하면 H(x) = F(x)+x 으로 만들어 그림과 같은 형식의 구조를 만들 수 있다. 입력 x가 y에 매핑하는 것보다 shortcut 형식의 입력 x가 0에 매핑하도록 하면 연산량의 증가없이 입력 x의 작은 움직임도 쉽게 검출 할 수있다. 또한 레이어를 건너 뛰며 입력과 출력이 연결되어 순전파와 역전파 연산 또한 단순해져서 깊은 망도 최적화 할 수 있게 되며 깊은 망으로 인한 정확도도 개선되는 효과를 얻는다. 이와같이 잔차(Residual)를 0에 수렴하게 학습 하는 network라고 하여 Resnet이라고 표현하게 된다.
 
 
BottleNeck Architecture
-----------------------

<img src="/paper_review/resnetpaper/image/4.JPG" width="80%" height="80%" title="img1" alt="img1"></img> 
   
ResNet 50 부터는 연산량의 줄이기 위해 Residual Block 내에, 1x1, 3x3, 1x1 Convolution Layer 구조로 되어있다. GoogleNet의 Inception 구조와 동일하다. 1x1 Convolution layer에서 dimension을 줄여 Feature map의 갯수를 줄였다가 3x3을 거친 후, 1x1 Convolution layer에서 dimension을 다시 늘려준다. 이 과정이 병목(Bottleneck)같다 하여 Bottleneck layer라고 부른다.   

 
Projection Shortcut
-----------------------

<img src="/paper_review/resnetpaper/image/5.JPG" width="80%" height="80%" title="img1" alt="img1"></img> 

Convolution layer의 feature map의 크기를 줄일때 pooling을 거의 사용하지않고 Covolution 연산의 stride 를 2로 하였다. feature map의 크기가 반으로 작아지는 경우 출력 featuremap의 갯수는 2배가 된다. 그럴경우 H(x) = F(x)+x 연산시 차원의 크기가 맞지 않아 덧셈 연산을 할 수 가 없다. 이럴때  Residual block 입력 x를F(x)에 바로 더하지 않고 1x1 Convolution layer를 통하여 featuremap의 갯수를 맞춰준다. 이를 Projection Shrotcut이라 한다.



실험내용
------------

>Model Architecture   
   
<img src="/paper_review/resnetpaper/image/3.JPG" width="40%" height="40%" title="img1" alt="img1"></img>   

>Model depth 별 구조 비교   

<img src="/paper_review/resnetpaper/image/6.JPG" width="80%" height="80%" title="img1" alt="img1"></img>   

>plain Net과 Resnet 비교   

<img src="/paper_review/resnetpaper/image/8.JPG" width="80%" height="80%" title="img1" alt="img1"></img>   

위 그림은 ImageNet Datasetd의 Plain network와 Restnet network를 비교 결과이다 .(bold curve  = val err) Planenet은 깊이가 깊어지면 더 높은 train,val error를 보인 ResNet Model의 경우 망이 더 깊은 ResNetdl이 더 좋은 성능을 보임을 보여준다.

>Model 별 top1, top err 비교  

<img src="/paper_review/resnetpaper/image/7.JPG" width="60%" height="60%" title="img1" alt="img1"></img>   

단일 모델 비교 결과는 152-layer의 경우 top-5 error율이 4.49% 까지 떨어지는 쾌거를 이루었다. 과거 어떤 모델보다 좋은결과를 얻었음을 보인다.
이후 1000-layer에 대해서도 최적화에 별 어려움이 없었음을 보여줬고 1202 layer에서는 Dataset의 부족으로 overfiting이 발생하여서 결과가 조금 떨어지는 것을 확인하였다. 이를 위해 reguarization 기법을 적용하여 성능이 올라갈 것을 예상한다.
   
Code 구현
----------
#### tesnorflow2.0 : <https://github.com/AhnYoungBin/Resnet_tensrorflow>    
#### pytorch : <https://github.com/AhnYoungBin/Resnet50_pytorch>
