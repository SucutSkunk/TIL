# TIL: Attention의 Query, Key, Value

## 1. Attention은 dictionary의 soft 버전이다

일반 dictionary는 query와 key가 **정확히 일치**하는 항목 하나의 value만 꺼냅니다(hard 검색). Attention은 query와 **모든 key의 유사도**를 계산해, 그 비율만큼 value를 **가중합**합니다.

| 구분 | Dictionary (hard) | Attention (soft) |
|---|---|---|
| 매칭 방식 | 정확히 일치하는 하나 선택 | 모든 key와의 유사도 계산 |
| 결과 | 해당 key의 value 하나 | 유사도 비율로 모든 value를 가중합 |
| 미분 가능성 | 불가능 (선택이 불연속) | 가능 (softmax로 연속) |

$$\text{Attention}(Q,K,V) = \text{softmax}\!\left(\frac{QK^\top}{\sqrt{d_k}}\right)V$$

- softmax 덕분에 미분이 가능하므로, "어디를 볼지"까지 역전파로 학습된다.
- 표준 구조에서 V는 항상 **K와 같은 위치(인덱스)의 토큰**에서 나온다. 가중치가 key 인덱스 j에 걸리고, 그 j의 v_j를 섞기 때문이다. 이것이 "key로 찾고 value를 가져온다"는 비유와 일치한다.

## 2. Q와 K를 동일하게 두지 않는 이유

W_Q = W_K = W라고 하면 점수가 다음과 같이 된다.

$$\text{score}(i,j) = x_i\, W W^\top x_j^\top$$

W Wᵀ는 대칭 행렬이므로 **score(i, j) = score(j, i)**가 된다. 이로 인해 다음 문제가 생긴다.

- **비대칭 관계를 표현할 수 없다.** "it → animal"은 강해야 하지만 "animal → it"은 같은 강도일 이유가 없다.
- **자기 자신에게 쏠린다.** W Wᵀ가 PSD라 같은 토큰끼리의 유사도가 커지기 쉽다.
- **Cross-attention에서는 구조적으로 불가능하다.** Q는 디코더, K는 인코더에서 와서 서로 다른 공간이다.

Q ≠ K이면 원점수 S = QKᵀ가 일반적으로 비대칭이다.

- S[i,j] = q_i · k_j ("i가 j를 보는 정도")
- S[j,i] = q_j · k_i ("j가 i를 보는 정도")

두 값은 서로 다른 벡터 쌍의 내적이라 값이 다르다. 그래서 attention map의 (i, j) 성분을 "i → j 방향의 주목도"로 읽는 **방향성 있는 분석**이 가능해진다.

### 주의할 점

- 비대칭의 원인은 "행렬 곱의 비가환성(A@B ≠ B@A)"이 아니라, **i의 query와 j의 key를 곱하느냐, j의 query와 i의 key를 곱하느냐**의 차이다.
- softmax는 행 단위로 정규화되므로, Q = K여도 가중치 행렬은 비대칭이 될 수 있다. 대칭성은 정규화 전의 **원점수 QKᵀ**로 따져야 한다.
- attention map이 모델의 판단 근거를 그대로 설명한다고 단정하기는 어렵다(예: "Attention is not Explanation" 논쟁).

## 참고

- `@`는 입력 차원에 따라 의미가 달라진다. 1차원 벡터끼리는 내적(스칼라), 2차원 행렬끼리는 행렬 곱이다.
- 행렬 곱 `Q @ K.T`의 (i, j) 성분은 i번째 행과 j번째 열의 내적이다.
