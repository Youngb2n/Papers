Feature Pyramid Networks for Object Detection
============================================
논문 링크 : <https://arxiv.org/pdf/1612.03144.pdf>   

Feature Pyramid
----------------
<img src="/FPN/image/1.JPG" width="80%" height="80%" title="img1" alt="img1"></img>    

Object Detection 분야에서 풀리지 않았던 고질적인 난제는 바로 작은 물체를 탐지해내기가 어렵다는 것입니다. 이를 위해서 이미지나 피쳐맵의 크기를 다양한 형태로 rescale하는 접근 방식이 있어왔습니다.    
> (a)는 입력 이미지 자체를 여러 크기로 resize 한 뒤, 각각의 이미지에서 물체를 탐지하는 기법입니다.(MTCNN)   
   
> (b)는 CNN 신경망을 통과하여 얻은 최종 단계의 피쳐맵으로 Object Detection을 수행하는 기법입니다.(YOLO)       
   
> (c)는 CNN 신경망을 통과하는 중간 과정에 생성되는 피쳐맵들 각각에 Object Detection을 수행하는 기법입니다.(SSD)   
   
> (d)는 feature pyramid network에서 방법입니다. 먼저 신경망을 통과하면서 단계별로 feature-map들을 생성하고  최상단 layer부터 반대로 내려오면서 feature를 합쳐준 뒤, Object Detection을 진행합니다. 이러한 방식을 통해서 상위 레이어의 추상화 된 정보와 하위 레이어의 작은 물체들에 대한 정보를 동시에 살리면서 Object Detection을 수행할 수 있게 됩니다. 

Feature Fusion
--------------
<img src="/FPN/image/2.JPG" width="80%" height="80%" title="img1" alt="img1"></img>    
FPN이 어떻게 상위 Feature-map과 하위 feature-map을 합쳐주는 지를 살펴보겠습니다. FPN은 피쳐맵이 레이어를 통과하면서 이미지 크기가 2배씩 작아집니다.  그래서 상위 feature-map과 하위 feature-map을 합치기 위해서는 이미지 크기를 맞춰줘야 한다. nearest neighbor upsampling 이라는 기법을 적용하여 상위 feature-map의 이미지 크기를 2배 키웠습니다.


Bottom-up Pathway
----------------
상향식 과정에서는 Backbone ConvNet의 Feedforward 계산입니다. 이는 스케일링 단계가 2인 여러 feature map으로 구성된 feature 계층을 계산합니다.
종종 동일한 크기의 출력 맵을 생성하는 레이어가 많이 있으며 이러한 레이어가 동일한 네트워크 단계에 있다.
특징 피라미드의 경우 각 단계마다 하나의 피라미드 레벨이 정의된다.

 
즉, 위로 올라가는 forward 단계에서는 매 레이어마다 의미 정보를 응축하는 역할을 하게 된다.
깊은 모델의 경우 가로, 세로 크기가 같은 레이어들이 여러개 있을 수 있는데
이 경우에 같은 레이어들은 하나의 단계로 취급해서 각 단계의 맨 마지막 레이어를 skip-connection에 사용하게 된다.
즉, 각 단계의 마지막 레이어의 출력을 특징 맵의 reference set으로 선택한다.
피라미드를 풍부하게 생성하며, 각 단계의 가장 깊은 레이어에는 가장 영향력 있는 특징이 있어야 한다.
특히 ResNet의 경우 각 단계의 마지막 residual block에서 출력되는 feature activation을 활성화한다.
conv2, conv3, conv4, conv5 출력에 대해 마지막 resi block의 출력을 C2, C3, C4, C5 라고 표시하고,
각 레이어에는 4, 8, 16, 32 픽셀의 strides가 설정된다. 
conv1은 큰 메모리를 차지하기 때문에 피라미드에 포함시키지 않는다. 

Top-down pathway and lateral connections
---------------------------------------

하향식 과정에서는 많은 의미 정보들을 가지고 있는 피쳐 맵을 2배로 Up-sampling 하여 더 높은 해상도의 이미지를 만드는 역할을 수행합니다.
여기서 skip-connection을 통해 같은 사이즈의 bottom-up 레이어와 합쳐서 손실된 지역적 정보를 보충하게 됩니다.
Top-down 과정에서 매 레이어마다 classifier / regressor 가 적용되는데, 같은 classifier / regressor 를 사용하기 때문에 채널이 전부 같고, 논문에서는 256 채널을 사용했다고 합니다. 따라서 skip connection을 합칠 때 1x1 레이어를 한 번 거쳐서 채널을 알맞게 바꿔줍니다.
즉, 업샘플링된 맵은 Element-wise addition에 의해 상향식 맵과 병합되는 과정을 거친다. 이 때 채널 수를 줄이기 위해 1x1 컨볼루션 레이어를 거치게 된다. 이 프로세스는 마지막 resolution map이 생성될 때까지 반복된다. 반복을 시작하기 위해 C5에 1x1 컨볼루션 레이어를 덧붙여 가장 정교한 resolution map을 생성합니다. 마지막으로 각 합쳐진 맵에 3x3 컨볼루션을 추가하여 최종 특징 맵을 생성합니다.
생성된 feature map은 각각 P2, P3, P4, P5라고 불리며, 각 동일한 Spatial Size 인 C2, C3, C4, C5에 대응된다.

Feature Pyramid Networks for RPN
--------------------------------
<img src="/FPN/image/3.JPG" width="80%" height="80%" title="img1" alt="img1"></img>    

Feature Pyramid Network 기법을 기존 Object Detection Model에 적용하여 실제로 잘 작동하는 지를 확인해보고자 했습니다. 논문에서는 Faster R-CNN의 RPN과 Classifier에 적용하여 실험을 진행하였습니다.   
Faster RCNN에서의 RPN은 먼저 pretrained 된 VGG를 통과한 피쳐맵을 전달받습니다. 해당 피쳐맵에 3x3 컨볼루션을 적용하여 Intermediate Layer라는 피쳐맵을 생성합니다. 이를 각각 1x1 컨볼루션을 양갈래로 적용하여 각 엥커 박스가 object인지 여부와 bounding box regression을 수행해줍니다. RPN에 FPN 기법을 적용하면 아래와 같은 구조를 취합니다.

<img src="/FPN/image/4.JPG" width="80%" height="80%" title="img1" alt="img1"></img>    

Feature Pyramid Networks for Fast R-CNN
---------------------------------------
<img src="/FPN/image/5.JPG" width="80%" height="80%" title="img1" alt="img1"></img>    

기존의 구조에서는 입력 이미지를 pretrained model을 통과시켜서 얻은 피쳐맵에 RoI를 사상시켰습니다. 하지만 앞서서 FPN을 통해서 우리는 P2, ..., P5에 이르는 다양한 크기의 피쳐맵을 생성하였습니다.
> RoI의 크기에 따라서 사상시킬 피쳐맵을 결정하는 수식   
   
<img src="/FPN/image/6.jpg" width="40%" height="40%" title="img1" alt="img1"></img>    
k 는 피쳐 맵의 번호입니다. 224란 크기는 pretrained model이 224x224 크기의 이미지를 학습한 것에서 따왔습니다. k0는 224x224 크기의 RoI가 들어왔을 때 몇 번째 피쳐맵에 사상시킬 것인지를 결정하는 수이며, 논문에서는 4로 설정되었습니다.
> 

실험내용
----------
> Object detection results using Faster R-CNN   
   
<img src="/FPN/image/8.JPG" width="80%" height="80%" title="img1" alt="img1"></img>  
여기서  lateral connection은 skip-connection과 동일한 말이다. 

> 모델 비교   
   
<img src="/FPN/image/7.JPG" width="80%" height="80%" title="img1" alt="img1"></img>    

Faster R-CNN모델을 BackBone으로 FPN의 lateral connection 과 Top-down 을 적용시킨 모델이 제일 좋은 성능을 보였다. 이외에도 RPN, Fast R-CNN,
등을 BackBone으로 실험을 하였지만 동일한 결과가 나왔다.
Resnet101을 기반으로 Faster R-CNN에 FPN을 입힌 모델이 CoCo 데이터세트에서도 다른 모델에 비하여 좋은 성능을 보여줌을 알 수 있다.
