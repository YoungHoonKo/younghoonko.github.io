---
title: 04. Machine Learning_RNN & LSTM (Time Series Prediction)
description: "머신러닝및실습(2) RNN, LSTM 정리: 순환 구조와 BPTT, RNN 입출력 구조 5가지, 장기 의존성 문제와 기울기 소실/폭발의 원인과 해결(ReLU, Xavier/He 초기화, LSTM/GRU, 기울기 클리핑), LSTM 게이트와 기억 셀, GRU, 그리고 서울 PM10과 Jena 기온을 LSTM으로 예측한 실습 결과"
Author: YoungHoon Ko
date: 2026-11-05 15:38:00 +0900
categories: [University(2026-First-Semester), Machine Learning]
tags: [machine-learning, deep-learning, python]
mermaid: true
image: /assets/img/lang/ml.png
---

## 학습 목표

- RNN의 순환 구조, BPTT, 입출력 구조(one-to-many 등)
- 장기 의존성 문제와 기울기 소실, 폭발의 원인, 해결 방법 (출제 가능)
- Xavier, He 초기화와 기울기 클리핑
- LSTM의 게이트(망각, 입력, 출력)와 기억 셀이 기울기 소실을 막는 이유, GRU와의 차이
- 강의 실습(SimpleRNN으로 IMDB 감성 분류)에서 해결책들이 쓰인 방식과 과적합
- LSTM으로 미세먼지(PM10)와 기온을 예측한 실습 결과 해석

> numpy 코드는 직접 다시 실행한 결과. LSTM 학습 결과는 실습 노트북(TensorFlow)에서 실행한 출력
{: .prompt-info }

---

## 1. RNN

- **RNN(순환 신경망)**: 은닉층의 출력이 다음 시각의 입력과 함께 다시 은닉층으로 들어가는 순환 구조
- 시계열, 문장 속 단어, 영상 속 프레임처럼 **순서와 상관관계가 있는 데이터**를 위해 고안됨
- 모든 시각에서 **같은 가중치(Wx, Wh)를 재사용**하며, 활성화 함수로 tanh를 씀

식으로 쓰면 $$h_t = \tanh(x_t W_x + h_{t-1} W_h + b)$$

```text
t=0 h=[0.2613 0.6099 0.3019 0.0593]
t=1 h=[-0.5533 -0.2666 -0.4614  0.1711]
t=2 h=[0.7492 0.2536 0.7084 0.0696]
t=3 h=[-0.9351 -0.4048 -0.5861  0.3325]
t=4 h=[ 0.4027 -0.6127  0.6261  0.5034]
```

- **BPTT(Backpropagation Through Time)**: 시간 방향으로 펼친 신경망에서 순전파 후 오차역전파를 하는 것

### RNN 입출력 구조

| 구조 | 입력 → 출력 | 예시 |
| --- | --- | --- |
| One-to-one | 1 → 1 | 일반 신경망과 같음 |
| One-to-many | 1 → 여러 개 | 이미지 캡셔닝 (이미지 한 장 → 문장) |
| Many-to-one | 여러 개 → 1 | 감성 분류, 이번 실습의 시계열 예측 (3시간 → 다음 1시간) |
| Many-to-many | 여러 개 → 여러 개 | 번역, 프레임 단위 비디오 분류 |
| Multi-layer | 위 구조를 여러 층 쌓음 | 표현력 증가 |

---

## 2. 장기 의존성 문제

- **장기 의존성 문제**: 시간적으로 멀리 떨어진 과거 정보가 마지막까지 전달되지 못하는 현상
- 원인은 BPTT에서 생기는 **기울기 소실 / 폭발**
  - 소실: tanh 미분(최대 1)과 **같은 가중치 행렬 Wh를 반복해서 곱함**
  - 폭발: Wh의 가장 큰 고유값이 1보다 크면 기울기가 지수적으로 커짐

Wh만 50번 곱해 보고(고유값 크기별), tanh 미분까지 곱한 경우를 비교했음.

```text
Wh 최대 고유값 0.5: 10시각 1.260e-02, 30시각 1.098e-08, 50시각 1.181e-14
Wh 최대 고유값 1.0: 10시각 1.509e+00, 30시각 7.200e-01, 50시각 7.120e-01
Wh 최대 고유값 1.5: 10시각 1.237e+02, 30시각 3.022e+05, 50시각 1.046e+09
고유값 1.0 + tanh 미분: 10시각 1.822e-01, 50시각 2.688e-06
```

| 상황 | 결과 |
| --- | --- |
| 고유값 0.5 | 50시각 뒤 1e-14 → **소실** |
| 고유값 1.5 | 50시각 뒤 1e+9 → **폭발** |
| 고유값 1.0 | 유지되지만, tanh 미분이 곱해지면 다시 1e-6으로 소실 |

### 해결 방법 (중요)

| 문제 | 해결 |
| --- | --- |
| 기울기 소실 | ① 은닉층 활성화를 ReLU, Leaky ReLU로 ② 적절한 가중치 초기화(**Xavier**: sigmoid, tanh / **He**: ReLU) ③ **LSTM, GRU** 사용 |
| 기울기 폭발 | **기울기 클리핑(Gradient Clipping)** 하나만 외우면 됨: 기울기의 크기(norm)가 임계값을 넘으면 비율을 맞춰 잘라냄 |

```python
def clip_grads(grads, max_norm):
    total = np.sqrt(sum(np.sum(g ** 2) for g in grads))
    rate = max_norm / (total + 1e-6)
    if rate < 1:
        grads = [g * rate for g in grads]
    return grads
```

```text
원래 norm: 130.0 -> 클리핑 후: [array([1.1538, 1.5385]), array([4.6154])] norm 5.0
```

방향은 그대로 두고 크기만 5로 줄어듦.

### Xavier와 He 초기화

- **Xavier**: 입력, 출력 노드 수를 함께 보고 분산을 정함. Normal이면 식으로 쓰면 $$\sigma = \sqrt{\frac{2}{n_{in} + n_{out}}}$$, Uniform이면 범위가 $$\pm\sqrt{\frac{6}{n_{in} + n_{out}}}$$
- **He**: ReLU용. **입력 노드 수만** 사용. Normal이면 $$\sigma = \sqrt{\frac{2}{n_{in}}}$$, Uniform이면 $$\pm\sqrt{\frac{6}{n_{in}}}$$

256개 노드를 10층 통과시킨 뒤 활성값의 표준편차:

```text
std 0.01   10층 뒤 활성값 표준편차: 1.0625e-08
Xavier     10층 뒤 활성값 표준편차: 2.2773e-01
He + ReLU  10층 뒤 활성값 표준편차: 8.6657e-01
```

표준편차 0.01로 작게 초기화하면 신호가 1e-8까지 사라지지만, Xavier(tanh)와 He(ReLU)는 깊은 층까지 신호가 유지됨.

### 강의 실습: SimpleRNN으로 IMDB 리뷰 감성 분류 (강의 자료의 실행 결과)

IMDB 영화 리뷰가 긍정(1)인지 부정(0)인지 맞히는 **many-to-one** 문제. 실습 노트북 파일은 없어서 강의 자료에 실린 코드와 출력으로 정리했음.

| 단계 | 내용 |
| --- | --- |
| 데이터 | `imdb.load_data(num_words=1000)`: 빈도 상위 1,000개 단어만 사용 (전체 88,584개), 1,000위 밖 단어는 2로 바뀜. 학습, 테스트 각 25,000개 중 5,000개, 1,000개만 사용 |
| 단어 확인 | `get_word_index()`로 {단어: 순위} 사전을 받아 {순위: 단어}로 뒤집음. 0 ~ 3은 특수 토큰이라 순위에 **+3**을 해야 문장이 제대로 복원됨 |
| 길이 맞추기 | `pad_sequences(maxlen=100)`: 100보다 짧으면 0으로 채우고 길면 앞부분을 잘라 **뒤쪽 100단어**만 남김 |
| 입력 표현 | `to_categorical`로 단어 번호를 길이 1,000의 one-hot 벡터로 → (5000, 100, 1000) |
| 모델 | `SimpleRNN(128, return_sequences=True) → SimpleRNN(128) → Dense(1, sigmoid)` |

```python
cell_size, timesteps, feature = 128, 100, 1000
model = Sequential(name='IMDB_RNN')
model.add(SimpleRNN(cell_size, kernel_initializer='he_uniform',
                    input_shape=(timesteps, feature), activation='relu', return_sequences=True))
model.add(SimpleRNN(cell_size, kernel_initializer='he_uniform', activation='relu'))
model.add(Dense(1, activation='sigmoid'))
model.compile(loss='binary_crossentropy',
              optimizer=Adam(learning_rate=0.001, clipnorm=1),   # 기울기 클리핑
              metrics=['accuracy'])
model.fit(train_seq, t_train, epochs=20, batch_size=64)
```

이 모델에 앞에서 본 해결책이 모두 들어가 있음.

- 기울기 소실 대책: 활성화 함수를 tanh 대신 **ReLU**, 그에 맞춰 **He Uniform** 초기화 (SimpleRNN 기본값은 tanh와 Xavier Uniform인 `glorot_uniform`)
- 기울기 폭발 대책: `clipnorm=1`로 **기울기 클리핑**
- `return_sequences=True`: 첫 RNN은 100개 시각의 출력을 모두 다음 RNN으로 넘기고, 두 번째 RNN은 마지막 출력 하나만 냄
- 매개변수: 128 × (128 + 1,000) + 128 = 144,512, 128 × (128 + 128) + 128 = 32,896, Dense 129 → 합계 177,537

```text
Epoch 1/20  - loss: 0.6873 - accuracy: 0.5310
Epoch 3/20  - loss: 2.0921 - accuracy: 0.7072
Epoch 5/20  - loss: 3.7323 - accuracy: 0.8006
Epoch 7/20  - loss: 21.7149 - accuracy: 0.8758
Epoch 8/20  - loss: 0.2401 - accuracy: 0.9278
Epoch 11/20 - loss: 0.0679 - accuracy: 0.9814
(자료에는 11 epoch까지 실려 있음)
test loss :  2.080683
test accuracy :  62.5 %
```

- 학습 정확도는 98%까지 오르지만 테스트는 **62.5%**로 심한 **과적합**. 5,000개의 짧은 데이터로 17만 개 매개변수를 학습했기 때문
- loss가 2.09, 3.73, **21.71**로 튀는 epoch이 있음. ReLU를 쓴 RNN은 같은 가중치를 100번 곱하면서 값이 커지기 쉬움 → 클리핑을 해도 불안정함
- 단어마다 길이 1,000의 one-hot 벡터는 거의 0이고 단어 사이의 의미 관계도 표현하지 못함. 보통은 `Embedding` 층으로 단어를 짧은 실수 벡터로 바꾸고, LSTM이나 GRU를 씀

---

## 3. LSTM

- RNN에 **게이트**를 추가해 장기 의존성 문제를 해결한 구조
- 게이트의 열림 정도는 sigmoid로 만든 0 ~ 1 사이 값(1이 완전 개방)
- **기억 셀 c**: 시각 t까지 필요한 정보를 저장하는 LSTM 내부 전용 기억. 다음 층으로는 은닉 상태 h만 나감

| 구성 | 식 | 역할 |
| --- | --- | --- |
| 망각 게이트 f | sigmoid | 이전 기억 셀에서 불필요한 기억을 얼마나 잊을지 |
| 새 기억 후보 g | tanh | 새로 추가할 정보 |
| 입력 게이트 i | sigmoid | 새 정보 g를 얼마나 반영할지 |
| 출력 게이트 o | sigmoid | 기억 셀 중 얼마나 은닉 상태로 내보낼지 |

식으로 쓰면 $$c_t = f \odot c_{t-1} + g \odot i$$ 이고, $$h_t = o \odot \tanh(c_t)$$

```text
forget f: [0.3023 0.4174] input i: [0.5397 0.5268] output o: [0.5467 0.5187]
candidate g: [ 0.2503 -0.1393]
c_t = f*c_prev + g*i = [ 0.2863 -0.1986]
h_t = o*tanh(c_t) = [ 0.1524 -0.1017]
기억 셀 역전파 dc_prev = dc * f -> [0.3023 0.4174] (매 시각 다른 f를 원소별로 곱함)
```

### 기울기 소실이 잘 일어나지 않는 이유

- 기억 셀의 역전파는 **`+` 노드와 `×` 노드만** 지남
- `+`는 기울기를 그대로 흘려보냄
- `×`는 행렬곱이 아니라 **원소별 곱(아다마르 곱)**이고, 매 시각 **다른 망각 게이트 값**을 곱함 → RNN처럼 같은 Wh가 반복해서 곱해지지 않음
- 망각 게이트가 1에 가까우면 기울기가 거의 줄지 않고 전달됨

### GRU

- LSTM의 많은 매개변수와 긴 계산 시간을 줄이려고 만든 구조. 기억 셀 없이 은닉 상태 h 하나만 씀
- **리셋 게이트 r**: 이전 은닉 상태를 얼마나 무시할지 (r = 0이면 과거를 완전히 무시)
- **업데이트 게이트 z**: 과거 정보와 새 정보를 어떤 비율로 섞을지 (LSTM의 망각 + 입력 게이트 역할)

GRU를 식으로 정리하면 (⊙는 원소별 곱)

- 리셋 게이트: $$r = \sigma(x_t W_x^{(r)} + h_{t-1} W_h^{(r)} + b^{(r)})$$
- 업데이트 게이트: $$z = \sigma(x_t W_x^{(z)} + h_{t-1} W_h^{(z)} + b^{(z)})$$
- 새 은닉 상태 후보: $$\tilde{h} = \tanh(x_t W_x + (r \odot h_{t-1}) W_h + b)$$
- 최종 은닉 상태: $$h_t = (1 - z) \odot h_{t-1} + z \odot \tilde{h}$$

z가 1에 가까우면 새 정보로 덮어쓰고, 0에 가까우면 과거를 그대로 유지함.

```text
PM10 LSTM(128, 입력 10): 71168 / LSTM(128, 입력 128): 131584
Jena LSTM(128, 입력 14): 73216
같은 크기 SimpleRNN: 17792 / GRU: 53760 / LSTM: 71168
```

- LSTM은 게이트 4개, GRU는 3개라 GRU가 약 25% 적음
- **데이터가 적으면 GRU, 많으면 LSTM**이 유리할 수 있음

---

## 4. 실습 1: 서울 PM10 예측 (실습 노트북 결과)

[01편](/posts/Machine_Learning_01/)에서 만든 2020년 서울 대기 + 기상 데이터(8,783시간, 10개 변수)로 **3시간을 보고 다음 1시간의 PM10**을 예측.

1. window sliding으로 X 8,780 × 30, 정답 t 8,780 × 1
2. `train_test_split(test_size=0.2, shuffle=False)`: 시계열이라 **섞지 않고** 앞 80%(약 9.6개월)로 학습, 뒤 20%로 평가
3. (샘플, 30)을 **(샘플, timesteps 3, feature 10)**으로 reshape (LSTM 입력은 3차원)
4. `LSTM(128, return_sequences=True) → LSTM(128) → Dense(1)`, loss MSE, 지표 RMSE, Adam(0.001), 50 epoch

- `return_sequences=True`는 모든 시각의 출력을 다음 LSTM으로 넘기기 위해 필요함 (두 번째 LSTM은 마지막 시각만 출력 → many-to-one)
- 매개변수: 4 × (128 × (128 + 10) + 128) = 71,168, 두 번째 층 131,584, Dense 129, 합계 202,881

```text
Epoch 1/50 - loss: 489.2421 - root_mean_squared_error: 22.1188
Epoch 50/50 - loss: 37.5785 - root_mean_squared_error: 6.1301
PM10 예측 :  71.1 / 정답 :  75.0
PM10 예측 :  70.31 / 정답 :  70.0
PM10 예측 :  68.45 / 정답 :  74.0
PM10 예측 :  71.69 / 정답 :  65.0
PM10 예측 :  65.94 / 정답 :  59.0
PM10 예측 :  61.93 / 정답 :  56.0
test loss(MSE) : 63.114708
test RMSE : 7.94
```

- 테스트 RMSE 7.94 → 한 시간 뒤 PM10을 평균적으로 약 8㎍/㎥ 정도 틀림
- train RMSE 6.13보다 커서 일반화 차이가 있고, 정답이 내려갈 때 예측이 한 박자 늦게 따라가는 모습(65 → 예측 71.69)이 보임. 직전 값을 거의 그대로 이어 가는 경향
- 테스트 구간이 11~12월이라 학습 구간과 계절이 달라 분포 차이도 있음

## 5. 실습 2: Jena 기온 예측 (실습 노트북 결과)

독일 Jena 기상 데이터(2009 ~ 2016, **10분 간격**, 14개 변수) 중 2016년(52,260행)만 사용. **20분(2개 시점)을 보고 다음 10분 뒤 기온**을 예측.

- (41,806, 2, 14) 학습 / (10,452, 2, 14) 테스트, 같은 LSTM 구조로 20 epoch (매개변수 204,929)

```text
Epoch 1/20 - loss: 14.5452 - root_mean_squared_error: 3.8138
Epoch 20/20 - loss: 0.5980 - root_mean_squared_error: 0.7733
온도(T(degC)) 예측 :  5.32 / 정답 :  5.94
온도(T(degC)) 예측 :  5.55 / 정답 :  6.43
온도(T(degC)) 예측 :  6.94 / 정답 :  7.56
온도(T(degC)) 예측 :  7.05 / 정답 :  8.14
test loss(MSE) : 0.638519
test RMSE : 0.8
```

- RMSE 0.8℃로 숫자는 좋아 보이지만, 10분 뒤 기온은 원래 현재 기온과 거의 같아서 쉬운 문제임
- 기온이 오르는 구간에서 예측이 **계속 0.6 ~ 1℃ 낮게** 따라감 → 추세를 늦게 반영
- 입력을 정규화하지 않았음(기압 약 1,000, 비습 약 3처럼 단위 차이가 큼). 정규화하면 학습이 더 안정적일 것

> 두 실습 모두 "직전 값을 그대로 예측"하는 단순 기준(persistence)과 비교하지 않았기 때문에, RMSE만으로는 LSTM이 실제로 얼마나 나은지 판단할 수 없음
{: .prompt-warning }

---

### 정리 노트에서 정정한 부분 정리

| 위치 | 원래 | 정정 | 이유 |
| --- | --- | --- | --- |
| Jena 실습 주석 | 이전 1시간, 1시간 뒤 예측 | 이전 20분(2개 시점), **10분 뒤** 예측 | 데이터가 10분 간격 |
| LSTM 장기 의존성 | 기울기 소실/폭발이 일어나지 않는다 | 일어나기 **어렵다** | 망각 게이트가 0에 가까우면 기울기는 여전히 줄어듦 |
| 초기화 | Xaiver | Xavier | 오타 |
| 옵티마이저 목록 | RMSEProp | RMSProp | 오타 |
| LSTM 1단계 | ht = tanh(ct) | ht = o ⊙ tanh(ct) | 출력 게이트를 곱해야 함 (2단계에서 추가되지만 최종식은 이것) |

---

## 최종 핵심 정리

1. RNN은 은닉 상태를 다음 시각으로 넘기는 순환 구조이며, 모든 시각에서 같은 가중치를 쓰고 tanh로 활성화함
2. 장기 의존성 문제는 BPTT에서 같은 Wh와 tanh 미분이 반복해서 곱해져 기울기가 소실되거나 폭발해서 생김
3. 기울기 소실은 ReLU, Xavier/He 초기화, LSTM/GRU로, 기울기 폭발은 기울기 클리핑으로 해결함
4. Xavier는 입력과 출력 노드 수를, He는 입력 노드 수만 사용하며 각각 sigmoid/tanh와 ReLU에 맞음
5. LSTM은 망각, 입력, 출력 게이트와 기억 셀로 정보를 선택적으로 기억하고 내보냄
6. 기억 셀의 역전파는 덧셈과 매번 다른 게이트 값의 원소별 곱만 거쳐 기울기 소실이 잘 일어나지 않음
7. GRU는 리셋, 업데이트 게이트만 써서 LSTM보다 매개변수가 약 25% 적고, 데이터가 적을 때 유리할 수 있음
8. 시계열은 섞지 않고 시간순으로 나누고 (샘플, 시점, 변수) 3차원으로 바꿔 넣으며, 성능은 단순 기준과 함께 비교해야 함

---

End.
