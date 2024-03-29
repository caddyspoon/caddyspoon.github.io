# [카카오 블라인드 채용 코딩테스트 2023] 표현 가능한 이진트리 (Python)


`표현 가능한 이진트리` 문제를 풀어보려고 합니다.

[프로그래머스](https://school.programmers.co.kr/)의 `2023 KAKAO BLIND RECRUITMENT` 문제팩 필터를 통해 찾아볼 수 있으며, [🚀여기](https://school.programmers.co.kr/learn/challenges?order=recent&page=1&partIds=37527)를 클릭하면 바로 이동할 수 있습니다.

제 코드는 가장 이상적인 해법이 아닙니다. 다만 저의 생각과 코드 작성의 방식을 공유하고자 글을 작성해봅니다.😋

---

## 문제

#### [🚀표현 가능한 이진트리](https://school.programmers.co.kr/learn/courses/30/lessons/150367)

*참고 링크 | [프로그래머스의 문제를 외부에 게시할 수 있나요?](https://programmers.zendesk.com/hc/ko/articles/360034546572-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EC%8A%A4%EC%9D%98-%EB%AC%B8%EC%A0%9C%EB%A5%BC-%EC%99%B8%EB%B6%80%EC%97%90-%EA%B2%8C%EC%8B%9C%ED%95%A0-%EC%88%98-%EC%9E%88%EB%82%98%EC%9A%94-)*

---

## 풀이

### 1. 해결 아이디어

#### 탐색: 변환된 십진수는 어떻게 표현될까?

포화 이진트리는 각 높이에 모든 노드들이 빼곡히 들어선 피라미드 모양의 이진트리입니다. 문제에서는 이진수로 표현된 이진트리의 경우, 비어있는 노드는 0으로, 실제 존재하는 노드는 1로 표기하고 있음을 밝히고 있습니다.

문제에서 주어진 `numbers` 배열의 숫자의 경우 이진수로 변환했을 때, 앞 자리에 얼마 만큼의 0이 존재하는지 알 수 없습니다.

문제에서 예시로 든 42를 예로 들어볼까요? 42를 이진수로 표현하면 **101010**입니다. 해당 수는 이진트리로 표현할 수 있다는 것을 문제에서 알려주고 있고 그 형태는 다음과 같습니다.

{{< figure src="MD_imgs/01_제일_작은_트리.png" title="111의 트리">}}

노드의 형태를 보고 직관적으로 이진수로 표현하면 **111**로 나타나는 트리입니다. 10진수로 나타내면 7에 해당하는 트리네요.

다시 42, 이진수 101010으로 돌아와 생각해 보겠습니다. 42는 이진트리로 나타낼 경우에 **0101010**이며 그림으로 나타나면 다음과 같이 나타낼 수 있습니다.

{{< figure src="MD_imgs/02_42의 트리.png">}}

손으로 그렸는데 깔끔하지 않게 됐어요. 그래서 귀엽기라도 해보자했습니다. 나쁘지 않네요. 낫 밷.

회색 웃는 점들이 비어있는 노드들입니다. 그림처럼 비어있는 노드를 추가하니 **111**로만 표현될 것만 같았던 트리가 다른 방식으로도 표현됐습니다. 지금은 **0101010**이네요. 네, 우리에게 익숙한 10진수로는 **42**입니다. 우주의 진리인 수인 만큼 트리 모양도 아름답네요.

여기에 빈 노드들로만 이루어진 한 층을 추가해도 트리가 만들 수 있지 않을까요? 자유민주주의 대한민국에서 안될 것 없죠. 해보죠, 까짓 거.

{{< figure src="MD_imgs/03_추가 트리.png">}}

이렇게 만들어진 트리는 **000100010001000**으로 나타낼 수 있습니다. 십진수로는 **2184**에 해당하는 숫자입니다. 이를 통해 우리가 알 수 있는 것은, **같은 모양의 트리도 다른 수를 나타낼 수 있다**는 것입니다. 여기서 하나의 의문이 생기는데요, ''**그렇다면 이진수로 변환된 수의 앞에 몇 개의 0을 붙여야 비어있는 노드가 포함된 트리 표현수가 될까?**'입니다. 

<br>

#### 발견: 포화 이진 트리로 표현하기

위의 시행에서 알아낸 사실이 있습니다. 이진수로 표현된 해당 십진수를 트리로 그리게 되면 그 형태는 포화 이진 트리로 만들어진다는 사실이죠. 해당 형태의 포화 이진 트리를 만들고, 비어있는 자식 노드를 0으로 바꾸게 되면 해당 숫자를 알아낼 수 있습니다.

달리 생각하면, 포화 이진트리를 통해 비어있는 노드가 포함된 트리를 구성할 수 있고, 이진수에서 가장 앞자리에 등장하는 1 앞에 등장하는 0의 개수도 알 수 있다는 의미이죠.

{{< figure src="MD_imgs/04_포화 이진 트리.png">}}

다음의 이진 트리는 **1111111**입니다. 42의 이진트리 표현 수인 **0101010**에서 비어있던 모든 노드를 채워놓은 형태로 볼 수 있겠네요. 42는 101010으로 바꾸어지며 여섯 자리의 2진수로 나타나게 된다는 것을 알 수 있습니다. 3층 짜리 이진 포화트리에서 뭔가가 부족한 트리라는 것을 우리는 확인할 수 있었죠. 

이와 같이 십진수의 해당 숫자를 2진수로 바꾸면 `지금 몇 개의 노드로 이루어진 트리`인지 알 수 있게 되고, 해당 노드 개수보다 큰 포화이진트리 중에 가장 가까운 포화이진트리가 목표 숫자의 형태로 만들 수 있는 포화 이진 트리임을 알 수 있습니다.

그럼 가장 `가까운 포화 이진 트리`는 어떻게 알 수 있을까요? 그건 지금까지 직접 그려본 결과 노드의 숫자를 보면 알 수 있었죠. 그리고 포화 이진 트리는 높이 h에 대해 2^(h + 1) - 1개의 노드 개수를 가집니다. 2층 짜리 포화 이진 트리는 3개, 3층 짜리 포화 이진 트리는 7개, ... 5층 짜리 이진트리는 63개의 노드 개수를 가지게 될 것입니다.

>**포화** 정 이진 트리에서, ![{\displaystyle l=2^{h}}](https://wikimedia.org/api/rest_v1/media/math/render/svg/0c75a8ef2ff68abf9711a5952953e0ee7ab98284)다. 따라서, ![{\displaystyle n=2^{h+1}-1}](https://wikimedia.org/api/rest_v1/media/math/render/svg/3558d5feb47029b11259917bbb81b6c14a5da484)이다. *(l은 리프 노드의 개수를 의미한다.)*
>
> *출처: [위키피디아, "이진 트리"](https://ko.wikipedia.org/wiki/%EC%9D%B4%EC%A7%84_%ED%8A%B8%EB%A6%AC#%EC%9D%B4%EC%A7%84_%ED%8A%B8%EB%A6%AC%EC%9D%98_%EC%86%8D%EC%84%B1)*

지금까지 예를 들었던 42로 다시 이 과정을 정리하면

```42로 이진트리를 만들 수 있을까? -> 이진수로 변환해보자 -> 101010이란 값을 얻었다! -> 현재 노드의 개수는 6개, 1앞에 필요한 0의 개수? -> 가장 가까운 포화 이진 트리의 노드 개수를 통해 알 수 있었지 -> 42의 노드 개수 6은 3(= 2^2 - 1)보다는 크지만 7(= 2^3 - 1) 보다는 작다! -> 즉 7개 노드로 이루어진 포화 이진 트리 1111111이 42가 그려질 트리의 형태이고 노드 한 개가 부족하니까 42는 0101010으로 나타낼 수 있다! ```

다음과 같은 과정을 통해 0까지 포함된, 즉 비어있는 노드까지 포함된 42가 나타낼 이진 트리의 형태를 알아낼 수 있습니다.

<br>

#### 통합: 가공한 자료를 통해 문제 해결하기

자 이제 십진수를 0까지 포함된 트리 표현식으로 해결할 수 있으니, 문제를 해결할 수 있습니다.

이제 주어진 이진 표현식으로 판별만 하면 되는데, 해당 트리 표현수가 실제 유효한 트리인 것은 어떻게 알 수 있을까요?

간단합니다. 예시에서 나타났듯, 자식 노드가 부모 노드가 존재할 때 존재하면 됩니다. 그림으로 나타내면 간단한 이야기입니다.

가령 5, 이진수로 나타냈을 때 101은 이진 트리로 나타낼 수 없습니다.

{{< figure src="MD_imgs/05_불가능 트리.png" width="300px" caption="트리가 가내수공업이라 좀 찌그러졌는데 이해해주세요.😘">}}

다음과 같이 부모는 없지만 자식이 있는 형태이기 때문입니다. 부모 없는 자식이 있을 수 없죠. 뭔가 패륜적 언사 같은데 오해하지 말고 들어주세요. 트리는 부모가 있어야 자식이 존재할 수 있습니다. 따라서 부모 노드 없는 자식노드로 이루어진 트리란 존재할 수 없습니다. 101로 나타내는 트리 형태는 부모 없이 자식들로만 이루어진 트리이기 때문에 존재할 수 없습니다.

자 그럼 이런 형태인지 아닌지만 판별해주면 되겠네요. 어떻게 하면 될까요? 바로 이 작업을 해주기 위해 0이 표시된 정 이진 트리로 만들기 위해 애썼던 것이죠.

다시 42를 가져와서 설명을 하자면, 현재 트리의 최상단 노드, 즉 **루트 노드는 트리 표현수의 가장 중앙에 위치하는 수**입니다. 0101010에서 루트노드는 네 번째 1입니다. 010**1**010에서의 까맣게 칠해진 1이죠. 이와 같은 작업을 반복해줍니다. 0101010에서 가장 루트 노드 1(010**1**010)은 부모와 자식이 이루어지는 = 자식이 있으며 부모가 존재하는 노드입니다. 

{{< figure src="MD_imgs/06_재귀트리1.png">}}

그림에서 보여주는 빨간색 부분이 트리가 가능함을 확인했고, 그 아래 존재하는 노란 자식들이 부모가 존재하는, 존재할 수 있는 트리임을 확인했습니다. 이것을 반복해줍니다. 

이제 `010 [1] 010`으로 쪼개어, 자식 트리들도 트리로 만들어지는 봅니다. `010 [1] 010`에서 [1] 왼쪽의 010은 아래 그림의 A트리가 될 것이고, 오른쪽 010은 B 트리가 될 것입니다.

{{< figure src="MD_imgs/07_재귀트리2.png">}}

공교롭게도 두 자식 트리 모두 010이네요. 부모가 존재하고 자식이 비어있는 구조입니다. 자식은 비어있어도 상관없습니다. 부모가 있다면 말이지요.

하지만 반대로 부모가 존재하지 않는데, 자식이 존재하면 안됩니다. 101(=십진수 5)와 같은 형태가 있으면 안되죠.

따라서 순차적으로 해당 이진트리를 표현하는 수의 중앙이 1이며, 자식 트리를 분리한 뒤 자식 트리를 다시 부모노드가 존재하는 트리인지 판별하는 과정을 반복해주면 됩니다. 또, 부모노드가 존재하지 않는데 자식 노드가 존재하는지 확인을 해주면 되겠습니다. 리프 노드에 닿을 때까지요. 전형적인 분할정복 방식의 코드가 될 것 같네요.

---

<br>

### 2. 코드

#### 1. 십진수를 이진수로 변환하기

위에서 이야기한대로 코드를 짜보겠습니다. 다음 함수는 십진수를 비어있는 0까지 모두 표현된 이진수(: sting)를 반환할 것입니다.

```python
# 현재 수 이진 값으로 만들기
def conv_to_bintree(num):
    # 이진 값으로 변환된 수
    target_num = bin(num)[2:]

    # 노드개수
    node_cnt = len(target_num)
    
    # 가장 가까운 포화 이진 트리 찾기
  
  	# 다음은 2의 지수가 될 변수
    # 'exp' stands for exponential
    exp = 0
    
    # (2의 제곱수 - 1)는 이진트리로 변환 했을 때 포화 이진 트리가 된다.
    # 가령 (2**2 - 1 = 3)의 경우 2층 포화 이진 트리, (2**3 - 1 = 7)의 경우 3층 포화 이진 트리가 된다.
    # 그러므로 현재 값보다 큰 가장 가까운 2의 제곱수로 만든 이진트리가 자식 노드가 몇 개 비워진 이진트리가 된다.

    while True:
        # 현재 값을 넘는 가장 가까운 2의 제곱 수인 경우
        if node_cnt < 2 ** exp:
            # 비어진 말단 최하단이자 최좌단의 노드 경우 0을 채워준다.
            # .zfill()의 자세한 설명은 아래 링크에
            return target_num.zfill(2 ** exp - 1)
        
        # 목표가 되는 수가 2**지수 보다 작을 경우 지수를 하나 더해줌
        else:
            exp += 1
```

*참고) [자릿수에 맞게 string에 0 채우는 메소드; str.zfill()](https://docs.python.org/3/library/stdtypes.html?highlight=zfill#str.zfill)*

<br>

#### 2. 이진트리 표현수가 유효한 트리 표현 방식인지 판별

이제 이진수로 표현된 수를 가지고 유효한 트리로 만들어지는 확인해봅시다.

```python
def verdict(target_num):
    stack = [target_num]
    
    while stack:
        num = stack.pop()
        # 현재 노드의 길이가 2보다 작으면 리프 노드
        if len(num) <= 2:
            continue
    
        # 현재 이진수로 표현된 트리의 가운데 값이 최상단 부모 노드
        parent_node = len(num) // 2

        # 부모노드가 0인데 자식노드가 존재하는 경우 -> 트리로 표현되지 않는 표현식
        # 따라서 이진 트리로 만들 수 없다고 판별하여 0을 반환
        if (num[parent_node]) == '0' and '1' in num:
            return 0

        # 현재 이진트리를 반으로 쪼개서 왼쪽 트리를 스택에 넣기
        stack.append(num[:parent_node])
        # 오른쪽 트리를 스택에 넣기
        stack.append(num[parent_node + 1:])

    # 순회가 끝난 경우 완성할 수 있는 트리이므로 1을 반환
    return 1
```

다음의 큰 두 가지 아이디어로 해결할 수 있는 문제였습니다. 전체 코드를 구성하면 다음과 같습니다.

<br>

#### 문제 해결 코드
```python
# 현재 수 이진 값으로 만들기
def conv_to_bintree(num):
    # 이진 값으로 변환된 수
    target_num = bin(num)[2:]

    # 노드개수
    node_cnt = len(target_num)
    
    # 가장 가까운 포화 이진 트리 찾기
  
  	# 다음은 2의 지수가 될 변수
    # 'exp' stands for exponential
    exp = 0
    
    # (2의 제곱수 - 1)는 이진트리로 변환 했을 때 포화 이진 트리가 된다.
    # 가령 (2**2 - 1 = 3)의 경우 2층 포화 이진 트리, (2**3 - 1 = 7)의 경우 3층 포화 이진 트리가 된다.
    # 그러므로 현재 값보다 큰 가장 가까운 2의 제곱수로 만든 이진트리가 자식 노드가 몇 개 비워진 이진트리가 된다.

    while True:
        # 현재 값을 넘는 가장 가까운 2의 제곱 수인 경우
        if node_cnt < 2 ** exp:
            # 비어진 말단 최하단이자 최좌단의 노드 경우 0을 채워준다.
            return target_num.zfill(2 ** exp - 1)
        
        # 목표가 되는 수가 2**지수 보다 작을 경우 지수를 하나 더해줌
        else:
            exp += 1

            
def verdict(target_num):
    stack = [target_num]
    
    while stack:
        num = stack.pop()
        # 현재 노드의 길이가 2보다 작으면 리프 노드
        if len(num) <= 2:
            continue
    
        # 현재 이진수로 표현된 트리의 가운데 값이 최상단 부모 노드
        parent_node = len(num) // 2

        # 부모노드가 0인데 자식노드가 존재하는 경우 -> 트리로 표현되지 않는 표현식
        # 따라서 이진 트리로 만들 수 없다고 판별하여 0을 반환
        if (num[parent_node]) == '0' and '1' in num:
                return 0

        # 현재 이진트리를 반으로 쪼개서 왼쪽 트리를 스택에 넣기
        stack.append(num[:parent_node])
        # 오른쪽 트리를 스택에 넣기
        stack.append(num[parent_node + 1:])

    # 순회가 끝난 경우 완성할 수 있는 트리이므로 1을 반환
    return 1


def solution(numbers):
    answer = []

    # 현재 숫자들의 이진 값들이 담길 리스트
    bin_nums = []
    for num in numbers:
        # 현재 수를 이진수로 변환하기
        bin_nums.append(conv_to_bintree(num))

    for bin_num in bin_nums:
        # 변환된 수를 이진트리로 만들 수 있는지 확인하기
        answer.append(verdict(bin_num))    

    return answer
```

---

'이렇게하면 이렇게 되나...?'' 하고, 손으로 그려가며 풀어본 문제였습니다. 복잡할 것 같은 문제도 코드로 적어내면 막상 길지 않은 경우가 있는데, 이 문제도 그런 경우였네요.

이번 문제는 이렇게 마무리하고 다음에는 제가 `2023 KAKAO BLIND RECRUITMENT`에서 풀면서 가장 재밌었던 [표 병합](/posts/2023/01/0131_카카오_블라인드_2023_05_표_병합/)의 해결 방법을 기록해보겠습니다. 

