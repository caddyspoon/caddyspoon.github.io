# [카카오 블라인드 채용 코딩테스트 2023] 표 병합 (Python)

`표 병합` 문제를 풀어보려고 합니다. 개인적으로는 2023 카카오 문제팩에서 제일 재미있게 해결한 문제입니다.

[프로그래머스](https://school.programmers.co.kr/)의 `2023 KAKAO BLIND RECRUITMENT` 문제팩 필터를 통해 찾아볼 수 있으며, [🚀여기](https://school.programmers.co.kr/learn/challenges?order=recent&page=1&partIds=37527)를 클릭하면 바로 이동할 수 있습니다.

제 코드는 가장 이상적인 해법이 아닙니다. 다만 저의 생각과 코드 작성의 방식을 공유하고자 글을 작성해봅니다.😋

---

## 문제

#### [🚀표 병합](https://school.programmers.co.kr/learn/courses/30/lessons/150366)

*참고 링크 | [프로그래머스의 문제를 외부에 게시할 수 있나요?](https://programmers.zendesk.com/hc/ko/articles/360034546572-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EC%8A%A4%EC%9D%98-%EB%AC%B8%EC%A0%9C%EB%A5%BC-%EC%99%B8%EB%B6%80%EC%97%90-%EA%B2%8C%EC%8B%9C%ED%95%A0-%EC%88%98-%EC%9E%88%EB%82%98%EC%9A%94-)*

---

## 풀이

### 1. 해결 아이디어

#### 주어진 대로 구현해보기

입력 값과 명령어를 주고 이를 수행할 수 있도록 하는 프로그램을 만드는 문제입니다. 이런 문제 해결은 개인적으로는 일단 코드를 만들어가면서 구현 과정을 확인해가면서 코드의 완성도를 높이는 편이 좋았습니다. 개발에서 ''이렇게 되지 않을까''라는 걱정에 미리 대비책을 모두 찾아 대비하는 경우보다, '일단 해보기'가 더 나은 결과를 만드는 경우가 있지 않았나요? 적어도 이 문제에서만큼은 그랬었던 것 같습니다.🕺

<br>

##### 1. UPDATE 구현하기

표에서 값을 찾고 새로운 값으로 바꾸어 줍니다. 같은 UPDATE에서도 두 종류의 업데이트가 존재하는데, 하나는 값을 기준으로 하는 UPDATE, 다른 하나는 기존 셀의 값을 기준으로 다른 모든 값을 찾아 바꾸어주는 UPDATE입니다.

표의 크기는 50 * 50입니다. 2,500개 칸을 가진 표네요. commands의 길이는 1,000개. 천 번의 명령 모두 모든 셀을 방문한다고 생각하면 2,500,000번입니다. 모든 셀 순회, 할 만 할지도...? UPDATE는 모든 셀을 돌면서 해당 하는 값을 찾고, 그 값을 찾아주는 방식으로 구현해봅시다.

<br>

##### 2. MERGE 구현하기

자 이제 MERGE를 구현합시다. 사실 이 문제의 핵심은 MERGE와 UNMERGE를 얼마나 잘 구현하는가입니다.

> 선택한 두 셀은 서로 인접하지 않을 수도 있습니다. 이 경우 (`r1`, `c1`) 위치의 셀과 (`r2`, `c2`) 위치의 셀만 영향을 받으며, 그 사이에 위치한 셀들은 영향을 받지 않습니다

MERGE는 다른 두 셀을 병합합니다. 주의사항은 문제에서의 예시는 인접한 두 셀을 기준으로 나타내고 있지만 실제로는 위치상 완전히 떨어져 있는 두 셀을 병합할 수도 있다는 사실이죠.

그리고 MERGE를 반복하는 경우를 생각해보면 알 수 있지만, 미리 병합된 셀을 다른 셀과 병합하면 2개 초과의 셀들이 하나의 셀로 합쳐질 수도 있습니다. 이를 주의해서 구현해야겠네요. 

> - 두 셀 중 한 셀이 값을 가지고 있을 경우 병합된 셀은 그 값을 가지게 됩니다.
> - 두 셀 모두 값을 가지고 있을 경우 병합된 셀은 (`r1`, `c1`) 위치의 셀 값을 가지게 됩니다.

병합된 셀들의 값을 어떻게 지정할 것인가에 대한 정의입니다. 무려 두 줄에 걸쳐서 설명하고 있네요. 분기 처리를 얼마나 잘 할 수 있는가를 묻는 문항이겠네요. 이는 간단하게 경우의 수를 세보면 금방 간단히 정의할 수 있습니다.

(r1, c1)에 존재하는 값을 value1, (r2, c2)에 존재하는 값을 value2라고 했을 때

1. value1 미존재, value2 미존재
   - 아무도 값이 없네요. 그냥 넘어갑니다.
2. value1 존재, value2 미존재
   - 값이 있는 value1의 값으로 합병합니다.
3. value1 존재, value2 존재
   - 두 셀 모두 값을 가지고 있을 경우 value1의 값으로 병합한다고 되어있네요. value1을 넣어줍시다.
4. value1 미존재, value2 존재
   - 값이 존재하는 value2의 값을 병합한 셀들에게 넣어줍니다.

정리하자면 네 가지의 케이스 중 4번 케이스를 제외하면 value1의 값을 각 셀들에 넣어주면 되겠네요. 즉, value2의 값이 있고 value1의 값이 없을 때만 value2의 값을 넣어주는 분기 처리를 해주고 그 이외에는 모두 value1을 넣어주도록 하겠습니다.

다시 어떻게 셀들을 병합할까에 대한 고민으로 돌아가 볼까요? MERGE된 셀들의 핵심은 하나의 셀이 변경되어도, 병합된 셀들의 값이 역시 변경되는 점이죠. 이 부분은 간단히 해결할 수 있습니다. 각 셀들의 값들을 참조형으로 만들면 됩니다.

표를 만들 때

```python
# 문제에서 값이 존재하는 경우 한 글자 이상의 단어라고 정의 되었기 때문에, 빈값의 경우 ''으로 표현 가능
table = [['' for _ in range(51)] for _ in range(51)]

# '' 대신, 다음과 같이 None으로 명시해주어도 된다.
table = [[None for _ in range(51)] for _ in range(51)]
```

 요런 식으로 생성하게 될텐데, 이 때 위처럼 비어있는 스트링이 아닌

```python
table = [[[''] for _ in range(51)] for _ in range(51)]

# 다음과 같이 명시해주어도 된다.
table = [[[None] for _ in range(51)] for _ in range(51)]
```

다음과 같은 리스트`[]`를 씌워준 형태를 셀의 값으로 지정하는 것입니다. 그렇게 될 경우 해당 셀의 값은 특정 스트링이 아닌 해당 주소값을 가진 리스트를 바라보게 됩니다. 따라서, 다른 셀에서 해당 리스트의 값이 변경되면, 다른 셀이어도 해당 리스트를 바라보고 있는 셀들의 값도 바뀌게 되는 것이지요.

<br>

{{< admonition note "접어두기 쪽문서" >}}
해당 내용에 익숙하지 않은 분들을 위해 상세 내용을 아래 접어두기 탭에 기록하겠습니다.

클릭해서 펼쳐주세요.*(커서가 포인터 모양으로 나오지 않아요!)*
{{</admonition>}}

<details>
<summary>가변 객체와 불변 객체, 상세내용 접기/펼치기</summary>
<div markdown="1">


---

파이썬의 불변형 객체의 경우 변수가 참조하는 또 다른 객체를 변경할 경우 같지 않을 수 있습니다.
쉽게 표현하면 아래와 같은 상황이 나타납니다. 자세한 사항은 주석을 참조해주세요.

```python
a = 10
b = a
print(a == b)
# True 출력

a = 11
print(a == b)
# False 출력
# a는 11, b는 10의 값으로 다르기 때문입니다.
```

<br>

하지만 가변형 객체의 경우 변수에 특정 객체를 참조해 할당했을 때, 참조 객체가 달라져도 같은 값을 나타냅니다.
아래와 같이 나타난다는 의미입니다. 마찬가지로 주석을 참조해주세요.

```python
a_list = [1, 2, 3]
# b_list는 a_list를 참조합니다.
b_list = a_list
print(a_list == b_list)
# True 출력

# 앗 a_list의 첫 번째 인덱스 값이 바뀌었습니다.
a_list[0] = 100	# 이제 a_list는 [100, 2, 3]입니다.
print(a_list == b_list)
# True 출력
# 왜냐하면 a_list가 바뀌면서 b_list의 0번 인덱스 값도 바뀌었으니까요.

print(b_list[0])
# 100 출력
# b_list[0]의 값도 1에서 100으로 바뀌었습니다.
# b_list 역시 [100, 2, 3]입니다.
```

<br>

파이썬의 공식 문서의 경우 다음과 같이 설명하고 있습니다.

> 형은 거의 모든 측면에서 객체가 동작하는 방법에 영향을 줍니다. 객체의 아이덴티디가 갖는 중요성조차도 어떤 면에서는 영향을 받습니다: 불변형의 경우, 새 값을 만드는 연산은 실제로는 이미 존재하는 객체 중에서 같은 형과 값을 갖는 것을 돌려줄 수 있습니다. 반면에 가변 객체에서는 이런 것이 허용되지 않습니다. 

> 예를 들어, a = 1; b = 1 후에, a 와 b 는 값 1을 갖는 같은 객체일 수도 있고, 아닐 수도 있습니다. 하지만 c = []; d = [] 후에, c 와 d 는 두 개의 서로 다르고, 독립적이고, 새로 만들어진 빈 리스트임이 보장됩니다. (c = d = [] 는 객은 객체를 c 와 d 에 대입합니다.)
> 
> *출처: [파이썬 공식 문서(한국어 버전), "데이터 모델, '객체, 값, 형'"](https://docs.python.org/ko/3/reference/datamodel.html#objects-values-and-types)*

---

<br>



</div>
</details>

<br>
따라서 각 셀을 불변형 객체가 아닌 가변형 객체로  지정합니다. 이를 통해 MERGE가 되었을 경우, 대상이 되는 셀이 변경되면 그 대상을 바라보는 셀들의 값 모두 변경될 것입니다.
<br>
<br>

##### 3. UNMERGE 구현하기
병합되어있던 셀을 찾아 모두 해제해줍니다. 효율성을 위해 여러가지 방법이 있겠지만.. 저는 그냥 모든 칸을 순회하면서 해당 셀이 병합된 셀인지 구분해서 해제해주도록 하겠습니다.

(r, c)로 지정된 셀을 '부모 셀'이라고 할 때, 나머지 셀들 중 어느 것이 해당 부모 셀을 따르고 있는 자식 셀인지 직접 비교하면 찾아낼 수 있겠지만, 여기서는 객체의 메모리 주소를 반환하는 파이썬의 내장 함수인 id()를 사용하겠습니다.

[아래 코드](/posts/2023/01/0131_카카오_블라인드_2023_05_표_병합/#3-unmerge-코드)를 통해 보시면 이해가 더 빠르실거에요.

*참고) [파이썬 내장 함수 'id()'](https://docs.python.org/ko/3/library/functions.html#id)*

<br>


##### 4. PRINT 구현하기

예쁘게 PRINT를  구현합니다.

---



### 2. 코드

<br>

##### 1. UPDATE 코드

최대한 직관적으로 만들어봅니다. 먼저 UPDATE는 같은 명령어임에도 두 종류가 있는데, 각 명령어는 세부 명령 코드의 개수가 다릅니다. 이를 통해 UPDATE 코드를 분기하고 문제에서 요구하는 수행을 해줍니다.

```python
# table은 표, com은 commands를 공백 기준으로 split하여 만든 리스트입니다.
# com = commands_리스트_원소.split(" ")[1:]
# 따라서 com은 "UPDATE"와 같은 명령어 키워드를 제외한 명령값입니다.
def update_cells(table, com):
    
    # 특정 값을 해당 칸에 지정하는 경우
    if len(com) == 3:
        r, c, value = com
        r, c = int(r), int(c)
        target_cell = table[r][c]
        target_cell[0] = value
        
    # 특정 셀의 값을 변경하는 경우
    elif len(com) == 2:
        v1, v2 = com
        for i in range(1, len(table)):
            for j in range(1, len(table[0])):
                if table[i][j][0] == v1:
                    table[i][j][0] = v2

    else:
        raise RuntimeError("UPDATE 매개변수 개수가 잘못 되었습니다.")
```

<br>

##### 2. MERGE 코드

위의 내용대로 코드를 구현해보겠습니다.

```python
def merge_cells(table, com):
    r1, c1, r2, c2 = map(int, com)  
    
    # 두 셀이 같은 칸이면 아무 것도 하지 않는다는 문제 조건
    if r1 == r2 and c1 == c2:
        return

    # 각 셀을 변수로 지정
    # 각 칸들은 ["rice"] 혹은 ["korean"]처럼 리스트로 이루어져 있습니다.
    cell1 = table[r1][c1]
    cell2 = table[r2][c2]

    # 각 셀은 리스트이므로, [0]으로 지정해줘야 해당 값이 지정됩니다.
    # 따라서 각 값들은 "rice", "korean"과 같은 실제 값이 됩니다.
    v1 = cell1[0]
    v2 = cell2[0]

    # 위에서 이야기한 네 번째 경우의 수에만 value2를 value1으로 지정할 수 있습니다.
    # parent_cell은 값을 전달하는 셀, child_cell은 부모의 값을 부여받는 셀입니다.
    # 자식 칸의 아이디로 대상이 될 자식들을 찾읍시다.
    if v2 and not v1:
        child_id = id(cell1)
        parent_cell = cell2
    else:
        child_id = id(cell2)
        parent_cell = cell1

    for i in range(1, len(table)):
        for j in range(1, len(table[0])):
          	# 자식 칸이면 부모 칸을 바라볼 수 있도록 할당합니다.
            if id(table[i][j]) == child_id:
                table[i][j] = parent_cell
```

<br>

##### 3. UNMERGE 코드

자식이 되는 셀 찾기, 그리고 그 칸에 새로운 주소값을 가진 새 리스트를 부여합니다.

```python
def unmerge_cells(table, com):
    r, c = map(int, com)

    value = table[r][c][0]
    # (r, c)칸과 같은 id 값을 가진 칸 = 병합된 칸을 찾기 위한 id
    target_cell_id = id(table[r][c])

    for i in range(1, len(table)):
        for j in range(1, len(table[0])):
            cell_id = id(table[i][j])
	
  					# id가 같은 칸, 즉 병합된 칸이라면
            if cell_id == target_cell_id:
        				# 새로운 리스트 부여
                table[i][j] = [None]
		# 기존에 값이 존재했다면
    if value:
      	# 원래 칸에 그 값을 넣어줍니다.
        table[r][c][0] = value
```

<br>

##### 문제 해결 코드

이와 같이 완성된 코드들을 합치면 다음과 같은 모습이 됩니다.

```python
# table은 표, com은 commands를 공백 기준으로 split하여 만든 리스트입니다.
# com = commands_리스트_원소.split(" ")[1:]
# 따라서 com은 "UPDATE"와 같은 명령어 키워드를 제외한 명령 정보를 담은 값입니다.
def update_cells(table, com):
    
    # 특정 값을 해당 칸에 지정하는 경우
    if len(com) == 3:
        r, c, value = com
        r, c = int(r), int(c)
        target_cell = table[r][c]
        target_cell[0] = value
        
    # 특정 셀의 값을 변경하는 경우
    elif len(com) == 2:
        v1, v2 = com
        for i in range(1, len(table)):
            for j in range(1, len(table[0])):
                if table[i][j][0] == v1:
                    table[i][j][0] = v2

    else:
        raise RuntimeError("UPDATE 매개변수 개수가 잘못 되었습니다.")

        
def merge_cells(table, com):
    r1, c1, r2, c2 = map(int, com)  
    
    # 두 셀이 같은 칸이면 아무 것도 하지 않는다는 문제 조건
    if r1 == r2 and c1 == c2:
        return

    # 각 셀을 변수로 지정
    # 각 칸들은 ["rice"] 혹은 ["korean"]처럼 리스트로 이루어져 있습니다.
    cell1 = table[r1][c1]
    cell2 = table[r2][c2]

    # 각 셀은 리스트이므로, [0]으로 지정해줘야 해당 값이 지정됩니다.
    # 따라서 각 값들은 "rice", "korean"과 같은 실제 값이 됩니다.
    v1 = cell1[0]
    v2 = cell2[0]

    # 위에서 이야기한 네 번째 경우의 수에만 value2를 value1으로 지정할 수 있습니다.
    # parent_cell은 값을 전달하는 셀, child_cell은 부모의 값을 부여받는 셀입니다.
    # 자식 칸의 아이디로 대상이 될 자식들을 찾읍시다.
    if v2 and not v1:
        child_id = id(cell1)
        parent_cell = cell2
    else:
        child_id = id(cell2)
        parent_cell = cell1

    for i in range(1, len(table)):
        for j in range(1, len(table[0])):
          	# 자식 칸이면 부모 칸을 바라볼 수 있도록 할당합니다.
            if id(table[i][j]) == child_id:
                table[i][j] = parent_cell

                
def unmerge_cells(table, com):
    r, c = map(int, com)

    value = table[r][c][0]
    # (r, c)칸과 같은 id 값을 가진 칸 = 병합된 칸을 찾기 위한 id
    target_cell_id = id(table[r][c])

    for i in range(1, len(table)):
        for j in range(1, len(table[0])):
            cell_id = id(table[i][j])
	
  					# id가 같은 칸, 즉 병합된 칸이라면
            if cell_id == target_cell_id:
        				# 새로운 리스트 부여
                table[i][j] = [None]
		# 기존에 값이 존재했다면
    if value:
      	# 원래 칸에 그 값을 넣어줍니다.
        table[r][c][0] = value
    
    
# 값이 있는 칸은 잘 출력해줍니다.
# 없으면 'EMPTY'를 출력합니다.
def print_cells(table, com):
    r, c = map(int, com)

    # table[r][c][0]에 값이 있으면 해당 값을, 아니면 "EMPTY"를 반환합니다.
    return table[r][c][0] or 'EMPTY'


# 명령어에 따라 실행 명령 종류를 정해주는 함수
def commit_commands(com_tokens_list, table, answer = []):
    command = com_tokens_list[0]
    com = com_tokens_list[1:]
    
    if command == "UPDATE":
        update_cells(table, com)
    elif command == "MERGE":
        merge_cells(table, com)
    elif command == "UNMERGE":
        unmerge_cells(table, com)
    elif command == "PRINT":
        answer.append(print_cells(table, com))
        
    return answer
    
    
def solution(commands):   
    # [None]으로 가득찬 51 * 51의 표를 만듭니다. 각 x, y축의 0번 줄은 사용하지 않습니다.
    table = [[[None] for _ in range(51)] for _ in range(51)]    
    
    answer = []
    for raw_com in commands:
      	# 명령어는 하나의 스트링으로 이루어져 있고, 문장 내 구분자가 공백이기 때문에
        # 각 명령어 토큰을 다음과 같이 분리합니다.
        com_tokens_list = raw_com.split(" ")
        answer = commit_commands(com_tokens_list, table)

    return answer

```

---

<br>

문제 해결 중에 많이 고쳐나가며 풀었던 문제입니다. 

해결 아이디어를 구현, 아이디어를 완벽히 구현하기 위한 개선하는 과정으로 완성된 코드에 다다를 수 있었습니다. 뚝딱뚝딱 🏗️

