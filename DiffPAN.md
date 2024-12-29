# U-Know-DiffPAN: An Uncertainty-aware Knowledge Distillation Diffusion Framework with Details Enhancement for PAN-Sharpening

논문의 주요 어젠더

위성 영상 처리 분야에서 **PAN-Sharpening** 라는 기법이 쓰임 $\rightarrow$ 이걸 향상시키기 위한 방안이 필요함

이에 대한 제안으로 **U-Know-DiffPAN**을 제안

1) PAN-Sharpening : 고 해상도 흑백 영상(PAN)과 저 해상도 멀티 스펙트럼 영상(MS)을 결합 </br>$\rightarrow$ 고 해상도 멀티 스펙트럼 영상을 만듬

2) 기존 모델의 문제점 : 세부 디테일+스펙트럼 왜곡문제 </br>$\rightarrow$ 논문에서 제안하는 방안 : 날리지 디스트리뷰션 + 확산기반 접근을 적용함

3) 모델이 확습 과정에서 발생하는 예측오차는 동적보정한다.


주요 개념

1) Diffusion model : 이미지 생성 및 복원에서 주로 사용되는 확산 모델로 Forward diffusion / Reverse diffusion 두 단계로 구성됨</br>
Forward diffusion : 노이즈를 추가하는 단계, </br>
Reverse diffusion : 노이즈를 제거하면서 복원</br>



## 초록 번역

기존의 PAN-Sharpening 기법들은 고주파 정보를 활용하는 데 제약이 있어 미세한 세부 정보를 복원하는 데 종종 어려움을 겪는다. 또한 확산(diffusion) 기반 접근법은 팬크로매틱(Panchromatic, PAN) 영상과 저해상도 다중 스펙트럼(LRMS) 영상을 충분히 활용하기 위한 적절한 조건부(conditioning)가 부족하다. 이러한 문제를 해결하기 위해, 우리는 PAN-Sharpening을 위한 세부 디테일 향상을 제공하는 불확실성(uncertainty) 기반 지식 증류 확산(knowledge distillation diffusion) 프레임워크인 U-Know-DiffPAN을 제안한다.

U-Know-DiffPAN은 불확실성 인지를 포함한 지식 증류(uncertainty-aware knowledge distillation)를 활용함으로써, 교사(teacher) 모델로부터 학생(student) 모델로 효율적으로 세부 특성(feature details)을 전이(transfer)한다. 우리 U-Know-DiffPAN의 교사 모델은 주파수 선택 주의(frequency selective attention)를 통해 주파수 디테일을 포착하여, 정확한 역(逆) 프로세스(reverse process) 학습을 돕는다. 또한 인코더에 PAN과 LRMS의 압축 벡터 표현을, 디코더에 웨이블릿(Wavelet) 변환을 적용함으로써 풍부한 주파수 정보를 활용할 수 있도록 한다. 이렇게 함으로써 용량이 큰 교사 모델은 주파수 정보가 풍부한 특징을 불확실성 맵(uncertainty map)의 도움을 받아 경량 학생 모델에 증류(distill)한다. 결과적으로, 교사 모델은 불확실성 맵을 활용해 학생 모델이 PAN-Sharpening 시 어려운 이미지 영역에 집중할 수 있도록 안내한다.

다양한 데이터셋에 대한 광범위한 실험 결과, U-Know-DiffPAN이 최근 최첨단 수준의 PAN-Sharpening 기법들과 비교하여 견고함과 우수한 성능을 보여준다는 사실이 입증되었다.

![Image](https://i.imgur.com/aIwfCLL.png)