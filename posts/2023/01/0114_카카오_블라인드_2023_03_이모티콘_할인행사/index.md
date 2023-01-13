# [카카오 블라인드 채용 코딩테스트 2023] 이모티콘 할인행사 (Python)


첫 번째 문제에 이어 다음으로 `이모티콘 할인행사` 문제 코드를 올려보려고 합니다. 

[프로그래머스](https://school.programmers.co.kr/)의 `2023 KAKAO BLIND RECRUITMENT` 문제팩 필터를 통해 찾아볼 수 있으며, [🚀여기](https://school.programmers.co.kr/learn/challenges?order=recent&page=1&partIds=37527)를 클릭하면 바로 이동할 수 있습니다.

제 코드는 가장 이상적이지 않고 잘 만들어지지도 않았습니다.🥲

---

## 문제

#### [🚀이모티콘 할인행사](https://school.programmers.co.kr/learn/courses/30/lessons/150368)

*참고 링크 | [프로그래머스의 문제를 외부에 게시할 수 있나요?](https://programmers.zendesk.com/hc/ko/articles/360034546572-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EC%8A%A4%EC%9D%98-%EB%AC%B8%EC%A0%9C%EB%A5%BC-%EC%99%B8%EB%B6%80%EC%97%90-%EA%B2%8C%EC%8B%9C%ED%95%A0-%EC%88%98-%EC%9E%88%EB%82%98%EC%9A%94-)*

---

## 풀이

### 1. 해결 아이디어

#### 문제 해결 방법을 고민해보기

예전에는 코딩 테스트 문제를 풀 때 `그리디 하게 문제 해결이 가능한가?`를 먼저 생각했습니다. 그런데 그렇게 접근하는 것보다는 `완전탐색이 가능한가?`를 먼저 접근했을 때 더 빨리 문제 해결을 할 수 있었습니다. 탐색 시간을 생각해보고 안되겠다 싶으면 그리디한 문제 해결법을 찾는 순서로 문제를 해결하는 게 더 효율적일 때가 많더라고요.

엄격하게 속도를 파악했을 때, 순수 파이썬은 1초에 1000만 루프의 일을 처리할 수 있다고 합니다. 그러니까 대략 1000만번 미만의 시행 횟수의 경우 무난하게 통과할 수 있다고 예측할 수 있겠습니다.

***참고** 파이썬의 처리 속도에 대한 아티클: [How Slow is Python Compared to C. ](https://peter-jp-xie.medium.com/how-slow-is-python-compared-to-c-3795071ce82a) (2020.07.13 작성)*

#### 시행횟수 짐작해보기

완전 탐색으로 문제를 해결한다고 할 경우 문제의 핵심이 되는 경우의 수는 `이모티콘 할인`의 가짓 수입니다. 문제에 의하면 이모티콘은 반드시 할인 가격으로 판매되는데 할인율의 경우 [10%, 20%, 30%, 40%]의 총 네 가지입니다. 그리고 고객들은 판매되는 가격 기준으로 이모티콘을 구매할지, 이모티콘 플러스를 구독할지 결정합니다.

고객의 수를 `n`으로, 이모티콘의 개수를 `m`이라고 할 경우 경우의 수는 `4^m * n `입니다. 그런데 문제 제한 사항을 보면 이모티콘 개수는 최대 7개입니다. m의 값만 고려할 때, 4의 7 제곱의 경우 16,384번의 시행을 하게 되는 것이고 고객의 최대 수 값 100을 고려해도 1,638,400번의 시행횟수이므로 충분히 해볼만한 가짓수로 볼 수 있겠네요. 그래서 모든 이모티콘의 할인 경우의 수를 통해 문제를 해결해볼만 하다고 보고 모든 가짓수를 탐색하는 식으로 문제를 해결해보겠습니다.

#### 모든 할인의 수를 어떻게 표현할 것인가?

모든 경우의 수를 찾는 것이므로 중복조합의 경우의 수를 고려하는 문제입니다. 여러 구현 방법이 있지만, 저는 재귀함수를 사용하는 편이 제일 편리하더라고요. 여기서는 먼저 재귀함수로 중복 조합을 표현하겠습니다.

그리고 파이썬에서는 `itertools` 모듈의 `product`가 자체적으로 중복조합을 제공합니다. *갓이썬... 그저 빛...⭐️*

---

<br>

### 2. 코드

#### 1. 중복조합 코드 짜기

할인율은 10/20/30/40의 총 네 가지 값을 가집니다. 이모티콘이 하나일 경우 해당 할인율이 적용되는 네 가지의 경우가, 두 가지일 경우 4 * 4의 16가지 경우의 수가 만들어지겠죠. 두 이모티콘 모두 10% 할인으로 경우의 수를 늘려간다고 한다면

(10, 10), (10, 20), (10, 30), (10, 40), (20, 10), (20, 20) ... (40, 20), (40, 30), (40, 40)

위와 같은 모든 할인 경우의 수를 살펴볼 것입니다.

재귀로 함수를 구현한다면 다음과 같은 코드가 될 것입니다.

```python
def 재귀함수(현재_인덱스 = 0, 현재_할인율_정보 = [0] * 이모티콘_개수):
		nonlocal 전체_할인률이_담기는_리스트

    # 현재 인덱스를 0부터 이모티콘 개수까지 이동할텐데, 현재 인덱스가 이모티콘 개수와 같다면 모든 경우의 수를 변경해준 결과일 것입니다.
    if 현재_인덱스 == 이모티콘_개수:
        return 현재_할인율_정보

    # 모든 할인율에 대해 
    # dc_rate는 [10, 20, 30, 40]을 넣은 값입니다.
    for 할인율 in dc_rate:
    		현재_할인율_정보[현재_인덱스] = 할인율	# 10, 20, 30, 40 중 하나를 넣어줍니다.
        
        # 다음 인덱스로 이동해줍니다.
        재귀함수(현재_인덱스 + 1, 현재_할인율_정보) # 할인율 정보 리스트도 같이 넣어주죠.
```



중복 조합이기 때문에 그리 복잡하지 않은 재귀 함수가 완성됩니다.

해당 코드로 문제를 해결하는 코드를 완성하면 다음과 같습니다.

solution() 함수 내부에 필요한 함수들을 넣었습니다.

```python
def solution(users, emoticons):
  	# 문제에서 주어진 할인율 조건 네 가지
    dc_rate = [10, 20, 30, 40]
    
    # 수많은 할인율의 경우의 수가 구현될 리스트의 초기 정보
    temp_arr = [0] * len(emoticons)
    
    # 주어진 할인율에 따라 계산하는 함수
    def cal(dc_info):
      	# 구독자 수
        sub_cnt = 0
        
        # 구독하지 않을 경우 이모티콘에 지불한 금액
        profit = 0
        
        # 각 사용자에 대해 할인율 경우의 수를 적용해 연산해 봄
        for user in users:
          	# 사용자 정보는 (구매할 할인율 기준, 자산)으로 구성됨
            dc_cut, budget = user
            
            # 이모티콘을 구매할 경우, 할인율이 적용된 이모티콘에 지불한 총액
            sum_cost = 0

            # 현재 사용자를 기준, 모든 이모티콘 금액을 순회
            for idx, emo in enumerate(emoticons):
              	# 현재 고객의 할인 등급율보다 현재 할인율이 더 높다면
                # 즉 구매 대상인 이모티콘이라면
                if dc_cut <= dc_info[idx]:
                  	# 누적 지출에 현재 할인된 이모티콘 구매 가격을 더해준다.
                    sum_cost += emo - int(emo * (dc_info[idx] / 100))

                # 누적 지출 금액이 자산 이상이 된다면
                # 즉 구매를 포기하고 구독을 원하게 된다면
                if sum_cost >= budget:
                  	# 구독자 수를 하나 더해준다.
                    sub_cnt += 1
                    break
            # 만약 무사히 순회가 종료됐다면
            # 구독하지 않고 이모티콘을 구매하는 것이므로
            # 지금 고객의 지출 금액을 전체 수익에 더해준다.
            else:
                profit += sum_cost
                        
        return [sub_cnt, profit]
                   
    # 결과 = [구독자 수, 이모티콘 판매 이윤]
    result = [0, 0]

    # 이모티콘의 모든 할인율을 만드는 중복조합 만들기
    def recur_sale(my_idx = 0, my_arr = temp_arr):
        nonlocal result

        # 현재 할인율이 완성되었다!
        if my_idx == len(emoticons):
          	# 현재 할인율 정보(my_arr)로 이윤과 구독자 수를 구해보자!
            crnt_result = cal(my_arr)
            
            # 만약 현재 시행까지의 최대 구독자 수보다 지금 시행의 구독자 수가 더 많다면
            if crnt_result[0] > result[0]:
              	# 지금 시행이 정답에 가깝다.
                result = crnt_result[:]
                
            # 구독자 수는 같은데, 이윤이 더 많다면
            elif crnt_result[0] == result[0]:
                if crnt_result[1] > result[1]:
                  	# 정답의 조건에 맞는다.
                    result = crnt_result[:]
            return
        
        # 위의 코드와 마찬가지로 중복조합 만들어주기
        for dc in dc_rate:
            my_arr[my_idx] = dc
            recur_sale(my_idx + 1, my_arr)
    
    # 실제 시행
    recur_sale()

    return result
```
<br>

#### 2. product 사용하기

파이썬의 product를 사용한다면 더 간편하게 문제를 해결할 수 있습니다.

for 루프를 통해 product를 사용하는 방법은 다음과 같습니다. 수를 세는 경우의 값을 넣어주고, 해당 값이 총 몇 번 반복이 되는지는 repeat 옵션을 통해 넣어줍니다.

가령 [10, 20, 30, 40]의 가지 수가 모두 3가지 경우가 하나 씩 값을 가질 때 `product([10, 20, 30, 40], repeat=3` 으로 표현해줍니다.

product 관련 코드는 51번째 줄부터입니다.

```python
from itertools import product

def solution(users, emoticons):
  	# 문제에서 주어진 할인율 조건 네 가지
    dc_rate = [10, 20, 30, 40]
    
    # 수많은 할인율의 경우의 수가 구현될 리스트의 초기 정보
    temp_arr = [0] * len(emoticons)
    
    # 주어진 할인율에 따라 계산하는 함수
    def cal(dc_info):
      	# 구독자 수
        sub_cnt = 0
        
        # 구독하지 않을 경우 이모티콘에 지불한 금액
        profit = 0
        
        # 각 사용자에 대해 할인율 경우의 수를 적용해 연산해 봄
        for user in users:
          	# 사용자 정보는 (구매할 할인율 기준, 자산)으로 구성됨
            dc_cut, budget = user
            
            # 이모티콘을 구매할 경우, 할인율이 적용된 이모티콘에 지불한 총액
            sum_cost = 0

            # 현재 사용자를 기준, 모든 이모티콘 금액을 순회
            for idx, emo in enumerate(emoticons):
              	# 현재 고객의 할인 등급율보다 현재 할인율이 더 높다면
                # 즉 구매 대상인 이모티콘이라면
                if dc_cut <= dc_info[idx]:
                  	# 누적 지출에 현재 할인된 이모티콘 구매 가격을 더해준다.
                    sum_cost += emo - int(emo * (dc_info[idx] / 100))

                # 누적 지출 금액이 자산 이상이 된다면
                # 즉 구매를 포기하고 구독을 원하게 된다면
                if sum_cost >= budget:
                  	# 구독자 수를 하나 더해준다.
                    sub_cnt += 1
                    break
            # 만약 무사히 순회가 종료됐다면
            # 구독하지 않고 이모티콘을 구매하는 것이므로
            # 지금 고객의 지출 금액을 전체 수익에 더해준다.
            else:
                profit += sum_cost
                        
        return [sub_cnt, profit]
                   
    # 결과 = [구독자 수, 이모티콘 판매 이윤]
    result = [0, 0]

   	# 아래와 같이 간단히 중복조합을 사용할 수 있습니다.
    for info in product(dc_rate, repeat=len(emoticons)):
        cnrt_result = cal(info)
        sub_cnt, profit = cnrt_result
       
        # 아래부터는 result를 판별하는 코드입니다.
        if sub_cnt > result[0]:
            result = cnrt_result

        elif sub_cnt == result[0]:
            if profit > result[1]:
                result = cnrt_result
    
    return result
```



---

<br>
