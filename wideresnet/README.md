Wide Residual Networks
======================
   
논문 링크 https://arxiv.org/pdf/1605.07146.pdf   

Introduction
------------

Deep Learning 계에서 ResNet 은 정말 성공적인 모델이다. 이 모델을 통해 더 많은 Layer를 쌓을수록 더 좋은 성능을 낸다는 사실을 알게 되었다.
이 논문은 Resnet Block에 대해 좀 더 심층적으로 살펴보도록 한다. CNN 의 Conv. 레이어 수는 지속적으로 증가하기 시작했다.
Alex, VGG, Inception, ResNet 의 단계를 거쳤다. 동시에 이미지 인식 task의 성능 향상도 이루어졌다.
하지만 망이 깊어지면서 학습도 점점 어려워졌다. 이를 해결하기 위해 다양한 기법들이 제시되었다.
(초기화 전략, 다양한 Optimizer, skip-connection, knowledge transfer, layer-wise 학습)
그중, ResNet(Residual Networks)는 shortcut connection을 통해 네트워크가 깊어지면 깊어질수록 
생기는 vanishing gradient를 해결하면서도 더 깊게 네트워크를 쌓을 수 있도록 설계되었다.


Wide vs Depth
-------------
> Block Archtecture 비교   
   
<img src="/wideresnet/image/1.JPG" width="60%" height="60%" title="img1" alt="img1"></img>   

ResNet은 shorcut connection을 통해 많은 layer을 학습 할 수 있도록 하였다.
하지만 망이 깊어지면 깊어질 수록 의미있는 정보(context)를 갖는 필터의 수의 비가 적어지는 문제가 발생하게 되었다.
때문에 저자는 Block의 수를 증가시키지 않고, Residual Block의 Chanel 증가시키는 방향으로 연구를 시도하였다.
이 논문에서는 Residual Block을 (a), (c)의 구조와 같이 3x3 Convolution Block이 두 개로 이루어진 경우를 B(3,3)으로 표기하였다.
이와 마찬가지로 (b)의 경우는 B(1,3,1) (d)는 B(3,1,3)으로 표기가 가능하다.

> ResNet B(3,3) Model Archtecture   
   
<img src="/wideresnet/image/2.JPG" width="80%" height="80%" title="img1" alt="img1"></img>   
B(3,3)의 경우 구조가 위와 같이 정의된다. K 는 widen factor, N은 블록의 수(각 Residual Block의 수)다.
이해를 돕기 위해 K가 2인 경우 아래와 같이 구현이 될 수 있습니다.

***

## Drop out
Dropout은 Coadaptive하고 overfitting을 막기 위해 많은 네트워크에 적용되어 왔다.
추가적으로 Internal Covariate Shift 이슈를 막기위한 Batch Norm 과 같은 방법들도 연구가되었는데,
이 방법들은 Regularizer의 효과도 볼 수 있다.
이 논문에서는 Residual Block의 컨볼루션 사이에 Dropout(Dorp rate 0.3)을 사용한다.




실험내용
-------  
> Width of residual blocks   
   
<img src="/wideresnet/image/3.JPG" width="40%" height="40%" title="img1" alt="img1"></img>   

폭(width)와 깊이(depth), 파라미터수를 비교하면서 실험을 해본 결과 동일한 depth 상에서는 k(Convolution layer 내에서 feature 수의 배수.
)가 클수록 우수하고 동일한 k 상에서는 depth가 클수록 우수하다. 하지만 비슷한 파라미터 수에서는 depth와 k의 중요도를 판정 지을 수 없었다.

> 다른 모델들과의 비교   
   
<img src="/wideresnet/image/4.JPG" width="60%" height="60%" title="img1" alt="img1"></img>   
<img src="/wideresnet/image/5.JPG" width="80%" height="80%" title="img1" alt="img1"></img>   

본 논문에서는 CIFAR10과 CIFAR100을 여러 모델을 통해 실험하였고, 
위의 결과와 같이 WRN(depth 28, k 10)이 test error 4로 가장 높은 성능을 보였다.
 WRN(depth 28, k 10) 모델이 pre-act-ResNet-1001 보다 0.92% 만큼 성능이 더 좋다.
표를 보면 사용되는 파라미터의 수가 WRN 이 훨씬 많지만 더 학습이 잘 된다는 결과다. 

> Dropout in residual blocks    
   
<img src="/wideresnet/image/6.JPG" width="60%" height="60%" title="img1" alt="img1"></img>   
<img src="/wideresnet/image/7.JPG" width="80%" height="80%" title="img1" alt="img1"></img>   

Dropout 을 residual block 에 적용해 본 결과 성능의 향상을 확인할 수 있었다.
cross-validation 을 이용해서 CIFAR 데이터에는 dropout 비율을 0.3 으로 정하였다.(SVHN은 0.4)
파라미터 수는 기존 ResNet 대비 많지만 성능의 향상을 확인할 수 있는거 같다.

