![Image](https://i.imgur.com/KjstW2P.png)

DAKD : 확산 모델을 활용한 SAR 기름 유출 세그멘테이션을 위한 데이터 증강 및 지식 종류

SAR??</br>
![Image](https://i.imgur.com/rOZCE6K.png)


## 초록

해양에서 발생하는 기름 유출은 심각한 환경적 위험을 초래하며, 조기 탐지가 필수적입니다. 합성 개구 레이더(SAR) 기반의 기름 유출 세그멘테이션은 다양한 조건에서 강력한 모니터링을 제공하지만, 제한된 레이블 데이터와 SAR 이미지에 내재된 스펙클 노이즈로 인해 도전에 직면하고 있습니다. 이러한 문제를 해결하기 위해, 우리는 (i) 확산 기반 데이터 증강 및 지식 증류(DAKD) 파이프라인과 (ii) 새로운 SAR 기름 유출 세그멘테이션 네트워크인 SAROSS-Net을 제안합니다.

우리의 DAKD 파이프라인은 SAR-JointNet이라는 확산 기반 모델을 통해 현실적인 SAR 이미지와 세그멘테이션 레이블을 생성하도록 학습하며, 두 가지 모달리티 간의 조합 분포를 효과적으로 모델링하여 이를 균형 있게 처리합니다. 이 파이프라인은 훈련 데이터셋을 증강하고, SAR-JointNet에서 생성된 소프트 레이블(픽셀 단위의 확률 맵)을 활용해 SAROSS-Net 학습을 감독함으로써 지식을 증류합니다.

SAROSS-Net은 노이즈가 많은 SAR 이미지에서 고주파 특징을 선택적으로 전송하도록 설계되었으며, 이를 위해 새로운 Context-Aware Feature Transfer(CAFT) 블록을 스킵 연결에 사용합니다. 우리는 SAR-JointNet이 현실적인 SAR 이미지와 정렬이 잘된 세그멘테이션 레이블을 생성할 수 있음을 입증했으며, 이를 통해 SAROSS-Net을 훈련할 수 있는 증강 데이터를 제공하여 일반화 성능을 향상시켰습니다. DAKD 파이프라인으로 학습된 SAROSS-Net은 기존의 SAR 기름 유출 세그멘테이션 방법을 큰 차이로 능가하는 성능을 보였습니다.


### Intro 1 fig 1

![Image](https://i.imgur.com/S2AJSfx.png)</br>

DAKD 파이프라인의 전반적인 구조를 보여주는 모델

SAR Joint Net : 확산 기반 모델 -> 노이즈를 입력 받아 `SAR` 이미지와 그에 상응하는 소프트 레이블(Soft Label)을 생성

소프트 레이블 : 0~1 사이의 애매한 값 $\leftrightarrow$ 하드레이블 : T/F</br>
이걸 쓰는 이유 : 기름 유출이라는 사건에 대해 이미지에 <span style=color:yellow>라벨을 확률로 기재</span>

<span style=color:cyan>Original Dataset (D)</span>>에서 학습한 모델을 기반으로 증강된 데이터셋 <spans style=color:Chartreuse>$D^a$<span> 생성

소프트 레이블 : $q_t$ $\rightarrow$ 픽셀 단위의 확률 맵<br>
기존의 세그멘테이션 마스크($y$)보다 더 풍부한 정보 제공

<span style=color:coral>손실함수</span>

$\mathcal{L}_{\text{ce}}$ : CrossEntropy Loss</br>
$\mathcal{L}_{\text{x}}$ : 생성 SAR이미지 $\leftrightarrow$ 원본SAR 간 이미지 차이 손실

![Image](https://i.imgur.com/VEyjueC.png)

날리지 디스트리뷰선은 원본 모델은 교사, 경량화모델은 학생으로 해서
교사의 추론 결과를 학생한테 계속 전달해서 학생을 가르치는것


### 그림 1의 전체 파이프라인 요약

`SAR-JointNet` : 데이터 증강 -> 학습 데이터 다양성을 높임, 소프트레이블 기반의 날리지 디스트리뷰션 수행</br>
<span style=color:Magenta>`SAROSS-NET`</span> : 증강된 데이터 + 소프트 레이블을 활용해 노이즈가 많이 낀 SAR 이미지에서도 기름유출을 효과적으로 디텍
SAR이미지 -> 원래 노이즈가 많이 낀 이미지 데이터를 출력함


</br></br></br>

# 1. 서론

해양 사고 확인에는 SAR방식을 씀 $\rightarrow$ 기름 유출을 잘 디텍함(어두운 점으로 디텍함)</br>
딥러닝 하는데 라벨데이터(기름 유출 데이터)가 부족함, SAR은 노이즈가 껴서 이미지가 구림</br>
$\rightarrow$ 세그멘테이션이 그닥?


---
<span style=color:yellow>논문이 제안하는 바</span>

SAR의 기름 유출 데이터 부족 $\rightarrow$ 기름 유출 세그멘테이션 작업을 위한</br> 
**확산 기반 데이터 증강 및 날리지 디스트리뷰션 기술 제안**

이에 대한 배경</br>
**확산 모델(diffusion model)** : 잘 된다 $\rightarrow$ 데이터 증강에서 활용가능성이 높다.

파이프라인 설계 : SAR-Joint Net -> 이건 해비한 모델(Teacher) -> 증강된 데이터를 학습함

<span style=color:Magenta>SAROSS-NET</span> : 이게 서비스 할 경량화 시킨 모델 -> 얘가 Teacher로부터 날리지 디스트리뷰션을 받아서</br>
노이즈가 많은 SAR 이미지에도 <span style=color:cyan>강건하게</span> 기름이 유출된 부분을 <span style=color:Chartreuse>세그멘테이션 함</span>

---

논문의 배경

SAR : 고주파 노이즈가 많음 $\rightarrow$ 정확한 세그 : 노이즈 특징으로부터 필요한 특징을 필터링 (추출)하는 것이 매우매우 중요</br>

**Context-Aware Feature Transfer (CAFT) 블록** : <span style=color:Magenta>SAROSS-NET</span>의 성능을 강화히기 위한 구성요소</br>
스킵 커넥션을 통해 MambaVision 인코더에서 디코더로 전달되는 인코딩된 특징을 선택적으로 전송

<span style=color:Magenta>SAROSS-NET</span> : 인코더로 `MambaVision`을 사용함

그러면 <span style=color:Magenta>SAROSS-NET</span>의 디코더는?? : CAFT (Context-Aware Feature Transfer) 블록을 포함한 무언가!

---

논문의 제안 요약

1) DKAD 파이프라인 : 데이터 증강 + 날리지 디스트리뷰선 -> 세그멘테이션 성능을 향상시키는 플랫폼 생성
2) 데이터 증강용으로 SAR JointNet 제안
3) SSROSS-NET의 디코더는 핵심블럭이 `CAFT` $\rightarrow$ 세그멘테이션 정확도를 높였음
4) 아무튼 잘됨



---

## 2.3. 날리지 디스트리뷰션(지식 증류) : Knowledge Distillation

KD(지식 증류, 날리지 디스트리뷰션)은 크고 복잡한 모델(Teacher model)로부터 더 작고 단순한 모델(student model)</br>
로 정보를 전달하는 기술

교사 $\rightarrow$ 부드러운 출력 예측(softened output predictions) $\rightarrow$ 학생</br>
부드러운 출력 : Soft label 소프트 레이블을 의미함</br>
정답 클래스만 전달한느게 아니라 다른 후보군 클래스의 상대적 확률도 같이 보내는 듯</br>

KD는 데이터 의존성을 해결하는데 사용 $\rightarrow$ 훈련데이터 없이 모델 학습이 가능하다는 뜻</br>
-> 교사 모델이 생 모델을 학습시키는 방법으로 합성 데이터를 생성해서 학습자료를 제공하기도 함

---


# 3. 방법

## 3.2.1 두 모달리티간의 확산 균형

**신호 대 잡음비(Signal-to-Noise Ratio, SNR)**를 기반 -> 그러니까 픽셀 단위 레이블이 아님

일단 배경 : SAR이미지는 고주파수 성분과 스펙클 노이즈가 포함된 복잡한 정보가 담긴 이미지임

따라서 이미지 단위로 확산시키면 잘 안됨 -> SNR 단위로 확산시켜야 정보전달이 잘 됨

![Image](https://i.imgur.com/EghMROf.png)

SAR Joint Net의 전체 구조임

상단 Label Net는 세그멘테이션 마스크를 생성하고, 하단 SAR-NET는 이미지를 생성함

x가 이미지의 데이터, y가 세그멘테이션 된 마스크 데이터

![Image](https://i.imgur.com/8NK8erZ.png)

![Image](https://i.imgur.com/NcJiIl3.png)

![Image](https://i.imgur.com/wlofoIb.png)

----

## 3.3. SAR Oil Slpill Segment Net

SAROSS-Nets는 맘바비전 인코더와 CAFT 블록으로 구성된 Sag Net임

![Image](https://i.imgur.com/7gomXbI.png)

MVE : 맘바 비전 인코더 -> SAR이미지를 입력 받아서 다양한 해상도에서 특징 추출</br>
CAFT : 스킵 연결을 통한 인코더 -> 디코더로 특징 전송(U-NET구조를 바탕으로)
![Image](https://i.imgur.com/fALpILT.png)

CD : Conv Decoder Block : 디코더 블록, CAFT에서 Feature를 받아서 Seg Map를 만들어냄</br>
-> 처음에는 작은 해상도 -> 끝단으로 갈 수록 더 높은 해상도

![Image](https://i.imgur.com/FTjcPVe.png)

---

### 3.3.1 CAFT

크로스 어탠션(Transfposed Attention)을 활용,</br>
저 해상도 특징맵 : Query</br>
고 해상도 특징 맵 : Key + Value로 활용함

트랜스포머의 Cross Attentnion을 사용했는데, 계산복잡도 감소를 위해서</br>
크로스 어탠션의 경량화를 위한 Transposed Attention을 적용함

![Image](https://i.imgur.com/8brpV60.png)

---

## 4. 실험결과

![Image](https://i.imgur.com/31TvfKc.png)
![Image](https://i.imgur.com/a74cZpI.png)

![Image](https://i.imgur.com/cVXnHGS.png)
![Image](https://i.imgur.com/K0fC8ch.png)

---

## 5. 요소 별 분석 연구

1) 4번 테이블dptj 학습 시 크로스 엔트로피 Loss을 사용한 경우에는 Soft Label이 하드레이블보다 더 좋음
![Image](https://i.imgur.com/ZdmKhTt.png)

2) CAFT 블록을 전체 DAKD 파이프라인에 추가했을 시 성능이 더 좋게 나옴
![Image](https://i.imgur.com/axXybHl.png)
인코더 블럭을 Manba vision, ResNet 두개로 놓고 했을 때 CAFT 유/무 성능테스트를 수행함
라벨링은 하드레이블, 소프트레이블 다 실험해봄

---

# 6. 결론

DAKD 파이프라인의 효과:

데이터를 증강하고, SAR-JointNet과 SAROSS-Net 간의 지식 전이를 효과적으로 수행.
균형 팩터 $b$와 소프트 레이블은 특히 데이터 부족 문제를 해결하는 데 중요한 역할을 함.
CAFT 블록의 중요성:
SAR 이미지의 노이즈를 억제하고, 중요한 경계 정보를 디코더로 전달하여 세그멘테이션 성능을 크게 향상.
