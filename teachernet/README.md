## A Comprehensive Overhaul of Feature Distillation


knowledge distillation는 더 큰 network(Teacher)의 감독하에 더 작은 network(Student)의 교육 과정을 돕는 방법을 말한다. 다른 압축 방식과 달리 Teacher와 Student network의 구조적 차이에 관계없이 network를 축소할 수 있다.

<p align="center"><img src="https://user-images.githubusercontent.com/45933225/79970850-8227d780-84ce-11ea-95e7-7dd09174ee43.png" width="50%"></p>

Teacher network의 Softmax 출력을 이용한 knowledge distillation(KD) 방법을 제안했다. 이 방법은 두 출력의 차수가 같기 때문에 모든 쌍의 network 아키텍처에 적용할 수 있다.FitNets [22]는 Student network가 Teacher network의 숨겨진 특징 값을 모방하도록 권장하는 방법을 제안했다

두 방법 모두 전달된 정보의 양을 증가시킴으로써 더 나은 distillation 성능을 보여준다. 그러나 FT[13]와 AB[7]는 Teacher의 특징적 가치를 변형하여 수행이 개선될 여지를 더 남겨둔다.

<p align="center"><img src="https://user-images.githubusercontent.com/45933225/79970870-88b64f00-84ce-11ea-8413-f156843a7c1a.png" width="70%"></p>

여기서 Tt , Ts, d 는 각각 Teacher transform, Student transform, distillation 특성 거리이며 이번 논문에ㄴ서는 여기서 하나 더 추가된 distillation 특성 위치 다양한 설계 측면의 조사를 통해 설계된 새로운 feature distillation loss을 제안함으로써 feature distillation 성능을 더욱 향상시킨다. 

feature 차원 Tt와 Ts를 각각 일치시키기 위해 Ft와 Fs를 변환한다. 변환된 feature 사이의 거리 d는 loss 함수 Ldistill로 사용된다

<p align="center"><img src="https://user-images.githubusercontent.com/45933225/79970888-910e8a00-84ce-11ea-997f-af2f1d103e9e.png" width="50%"></p>

Student network는 distillation loss Ldistill을 최소화하여 교육한다

feature distillation loss의 설계 측면은 Teacher transform, Student transform, distillation feature 위치 및 거리 함수의 4가지 범주로 분류된다.

      1. Teacher transform 
      
            A. Teacher를 변화시키는 Tt는 Teacher의 hidden feature를 전달하기 쉬운 형태로 바꾼다.
            feature distillation의 중요한 부분이며 또한 distillation에서 정보가 누락된 주요 원인이다.
            B. Teacher transform을 위해 Margin ReLU라고 불리는 새로운 ReLU 활성화를 사용한다. 우리의 Margin ReLU에서는 음성(부정) 정보가 억제되는 동안, 양성(유익한) 정보가 아무런 transform 없이 사용된다.
            
      2. Student transform
      
            A. teacher transform Tt와 동일한 기능을 사용한다.
            B. 1*1 convolutional 레이어를 Student transform으로 사용하여 Teacher와 feature 차원을 일치시킨다. 이 경우 Student의 feature 사이즈는 줄어들지 않고 오히려 커지기 때문에 정보가 누락되지 않는다.
            
      3. Distillation feature position
      
            A. distillation loss을 설계하여 pre-ReLU라고 하는 ReLU 함수 앞에 feature을 가져오게 한다. 양의 값과 음의 값은 변형없이 pre-ReLU 위치에서 유지된다.
            
      4. Distance function
      
            A. pre-ReLU 정보는 Teacher에서 Student로 전달되지만, pre-ReLU 기능의 음수 값에는 불리한 정보가 포함된다.
            B. negative region에대한 정보의 distillation를 건너뛰도록 고안된 부분 L2 거리라고 불리는 새로운 거리 함수를 제안한다.
            C. 우리가 제안한 방법에서 distillation 위치는 첫 번째 ReLU와 레이어 block의 끝 사이에 있다. 이 위치 지정은 Student가 Teacher가 ReLU를 통과하기 전에 보존된 정보에 도달할 수 있도록 한다. 

### Loss Function
Teacher의 값이 긍정적이면 Student은 Teacher와 같은 값을 생산해야 한다. 반대로 Teacher의 값이 부정적이면 Student은 0보다 작은 값을 만들어 뉴런의 활성화 상태를 동일하게 만들어야 한다.
Student의 가치를 0 이하로 만들기 위해서는 margin이 요구된다는 점에 주목했다. 따라서, 우리는 긍정적인 가치를 유지하면서 부정적인 margin을 주는 Teacher transform을 제안한다.

<p align="center"><img src="https://user-images.githubusercontent.com/45933225/79970930-a1bf0000-84ce-11ea-9f1b-bc3b35adfecc.png" width="100%"></p>

margin 값이 0보다 작다.우리는 이 기능을 Margin ReLU로 한다. Margin ReLU는 teacher의 부정적인 값보다 따라 하기 쉬운 negative Margin을 제공하도록 설계되었다. Teacher의 weight 값을 반영하지 않는 임의의 스칼라 값으로 margin을 설정했다.

Student transform의 경우, 1*1 convolution layer [22, 7]와 batch normalization layer로 구성된 regressor를 사용한다.

<p align="center"><img src="https://user-images.githubusercontent.com/45933225/79970966-ac799500-84ce-11ea-9918-823861be0138.png" width="50%"></p>

negative Teacher responses의 경우, Student response가 목표값보다 높으면 감소해야 하지만, Student response 목표값보다 낮으면, 그 가치와 상관없이 ReLU에 의해 똑같이 차단되기 때문에증가시킬 필요는 없다.

<p align="center"><img src="https://user-images.githubusercontent.com/45933225/79970985-b3a0a300-84ce-11ea-904b-171382603021.png" width="50%"></p>

T는 teacher의 feature을 위한 자리, S는 Student의 feature을 위한 자리. 우리가 제안한 방법은 Teacher로서는 margin ReLU mc(x)를, Student transform으로는 1*1 convolution layer로 구성된 regressor r(.)를, 거리 함수로서는 부분 L2 거리(dp)를 사용한다. 제안된 방법의 distillation loss는 다음과 같다.
 
<p align="center"><img src="https://user-images.githubusercontent.com/45933225/79971011-bdc2a180-84ce-11ea-923b-8c0716703686.png" width="50%"></p>

distillation loss Ldistill을 이용한 지속적인 distillation로 수행된다. 따라서 최종 loss 기능은 distillation loss과 task loss의 합이다.

<p align="center"><img src="https://user-images.githubusercontent.com/45933225/79971033-c3b88280-84ce-11ea-8ba7-21cd42e7bffc.png" width="50%"></p>

Task loss란 network의 task로 지정되는 loss을 말한다.


### Batch normalization
Teacher의 batch normalization layer의 모드는 정보를 distillation할 때 훈련 모드가 되어야 한다. 이를위해 1*1 conolutional layer 다음에 batch normalization layer를 붙여 Student transform으로 사용하고, Teacher로부터 얻은 knowledge을 training mode로 가져온다.
