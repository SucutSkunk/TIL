# Epoch, Iteration, Batch Size

> 학습 과정에서 "몇 개씩, 몇 번, 몇 바퀴" 학습하는지를 나타내는 세 가지 개념

---

## 1. 한 줄 정의

| 용어 | 의미 | 단위 |
|---|---|---|
| **Batch size** | 한 번의 gradient 계산(파라미터 업데이트)에 사용하는 데이터 개수 | 개수 |
| **Iteration** | batch 하나를 처리해서 파라미터를 **한 번 업데이트**하는 것 (= step) | 횟수 |
| **Epoch** | 전체 학습 데이터를 **한 바퀴** 다 보는 것 | 바퀴 수 |

- batch size는 **크기(개수)**, iteration은 **업데이트 횟수**, epoch은 **전체 데이터 반복 횟수**
- batch size와 iteration은 서로 연결되어 있지만 **같은 개념이 아니다**

---

## 2. 세 개념의 관계

$$
\text{1 epoch당 iteration 수} = \left\lceil \frac{\text{전체 데이터 수}}{\text{batch size}} \right\rceil
$$

$$
\text{총 iteration 수} = \text{epoch 수} \times \text{1 epoch당 iteration 수}
$$

- 나누어떨어지지 않으면 마지막 batch는 남은 개수만큼만 처리하므로 **올림**한다
- batch size와 1 epoch당 iteration 수는 **반비례** 관계

---

## 3. 예시

**조건**: 전체 데이터 1,000개, batch size 100, 5 epoch 학습

```
전체 데이터: [■■■■■■■■■■] 1,000개

batch 1  → 100개 → 업데이트 (iteration 1)
batch 2  → 100개 → 업데이트 (iteration 2)
...
batch 10 → 100개 → 업데이트 (iteration 10)
→ 1 epoch 완료

이것을 5번 반복 → 5 epoch
```

- 1 epoch당 iteration 수: 1,000 ÷ 100 = **10**
- 총 iteration 수: 5 × 10 = **50** (파라미터가 총 50번 업데이트됨)

### batch size를 바꾸면?

| 전체 데이터 | batch size | 1 epoch당 iteration |
|---|---|---|
| 1,000 | 1,000 | 1 |
| 1,000 | 100 | 10 |
| 1,000 | 50 | 20 |
| 1,000 | 1 | 1,000 |
| 1,050 | 100 | 11 (마지막 batch는 50개) |

---

## 4. Batch size에 따른 Gradient Descent 분류

| 방식 | batch size | 특징 |
|---|---|---|
| **Batch GD** | 전체 데이터 수 | gradient가 정확하고 안정적이지만, 1 epoch에 업데이트가 한 번뿐이라 느리고 메모리 부담이 큼 |
| **Stochastic GD (SGD)** | 1 | 업데이트가 매우 잦고 빠르지만, gradient의 노이즈가 커서 경로가 요동침 |
| **Mini-batch GD** | 1 < B < 전체 | 두 방식의 절충. 실무에서 가장 많이 사용 |

- 실무에서는 mini-batch 방식을 쓰면서도 관례적으로 **SGD**라고 부르는 경우가 많다
- batch size는 보통 GPU 메모리 효율을 위해 32, 64, 128, 256처럼 **2의 거듭제곱**으로 설정하는 경우가 많다

---

## 5. 코드로 보기 (PyTorch)

```python
from torch.utils.data import DataLoader

loader = DataLoader(dataset, batch_size=100, shuffle=True)  # batch size

for epoch in range(5):              # epoch: 전체 데이터를 5바퀴
    for x, y in loader:             # 한 바퀴 안에서 batch를 하나씩 꺼냄
        pred = model(x)
        loss = loss_fn(pred, y)

        optimizer.zero_grad()
        loss.backward()
        optimizer.step()            # ← 여기 한 번 = 1 iteration
```

- `len(loader)` = 1 epoch당 iteration 수
- 바깥 루프가 **epoch**, 안쪽 루프 한 바퀴가 **iteration**

---

## 6. 정리

- **Batch size**: 한 번에 **몇 개**를 보고 gradient를 계산하는가
- **Iteration**: 파라미터를 **몇 번** 업데이트했는가
- **Epoch**: 전체 데이터를 **몇 바퀴** 돌았는가
- 세 값 중 batch size와 epoch을 정하면 iteration 수는 자동으로 결정된다
- batch size, epoch 수는 학습 전에 사람이 정하는 값이므로 **hyperparameter**에 해당한다
