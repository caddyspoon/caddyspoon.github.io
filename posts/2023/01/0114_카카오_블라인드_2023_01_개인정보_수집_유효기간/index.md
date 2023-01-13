# [카카오 블라인드 채용 코딩테스트 2023] 개인정보 수집 유효기간 (JavaScript)


카카오의 2023년 블라인드 공개채용의 코딩테스트 문제가 공개되었습니다. [프로그래머스](https://school.programmers.co.kr/)의 `2023 KAKAO BLIND RECRUITMENT` 문제팩 필터를 통해 찾아볼 수 있습니다.

[🚀여기](https://school.programmers.co.kr/learn/challenges?order=recent&page=1&partIds=37527)를 클릭하면 바로 이동할 수 있습니다. 공개된 문제 중 몇 가지 문제를 풀어 코드를 올려보려고 합니다. 

제 코드는 가장 이상적이지 않고 잘 만들어지지도 않았습니다.🥲 저는 이렇게 짰다 정도로 포스팅하려고 합니다.

---

## 문제

#### [🚀개인정보 수집 유효기간 ](https://school.programmers.co.kr/learn/courses/30/lessons/150370)

*참고 링크 | [프로그래머스의 문제를 외부에 게시할 수 있나요?](https://programmers.zendesk.com/hc/ko/articles/360034546572-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EC%8A%A4%EC%9D%98-%EB%AC%B8%EC%A0%9C%EB%A5%BC-%EC%99%B8%EB%B6%80%EC%97%90-%EA%B2%8C%EC%8B%9C%ED%95%A0-%EC%88%98-%EC%9E%88%EB%82%98%EC%9A%94-)*

---

## 풀이

1번 문제로 자주 나오는 데이터 변환 문제입니다. 몇 가지 주의사항만 주의해주면 어려움 없이 해결할 수 있는 문제입니다.

### 1. 해결 아이디어

#### 날짜 환산해주기

일자를 오늘 기준으로 유효한지 파악하는 문제입니다. 먼저 `기준 일자 단위를 정합니다.` 시간의 최소단위가 일자이므로 `일 기준`으로 각 날짜들을 환산해줍니다. 단 제한사항으로 가장 이른 시작 연도는 2000년이며, 각 월은 28일입니다. 

따라서 연도는 2001년을 1, 2010년을 10 등의 단위 연도로 바꿔주기 위해 주어진 연도에 2000을 빼줍니다. 또한, 각 개월은 28일을 곱해서 일 수를 구해줍니다. 따라서 구하게 될 변환 날짜는 다음과 같습니다.

`환산 일자 = ((기준 연도 - 2000) * 12개월 * 28일) + 개월 * 28 + 일`

#### 정책 정리하기

각 정책들은 개월 수로 표현됩니다. 또한 정책의 이름은 스트링인 알파벳으로 주어집니다. 따라서 정책 이름을 키 값으로, 유효 개월 수를 밸류로 하는 객체로 접근하면 데이터 접근이 편할 것 같네요.

#### 기타 유의사항

정책 유효 기한은 만으로 1년입니다. 따라서 기준 일자의 일자에서 하나 부족한 날까지 유효합니다. 가령 유효 기간이 12개월이고 보관 시작일이 2021년 1월 `5`일이라면 2022년 1월 `4`일까지가 유효한 기간입니다. 이는 친절하게 문제해서도 알려주고 있네요.

<br>

### 2. 코드

따라서 코드를 작성하면 다음과 같습니다.

```javascript
// 연월일을 전달 받으면 이를 환산된 일자로 변환하는 함수
const convertDate = (date) => {
    const dateInfo = date.split(".").map(elm => Number(elm));
    return (dateInfo[0] - 2000) * 12 * 28 + dateInfo[1] * 28 + dateInfo[2];
}

const solution = (today, terms, privacies) => {
    const result = [];
    
  	// 변환된 오늘 날짜
    const _today = convertDate(today);
    
  	// 정책 이름 - 개월 수를 key-value로 하는 객체
    policyInfo = {};
    terms.map(term => {
        const [policyName, month] = term.split(" ");
      	/** month 값은 스트링이므로 Number로 변환해줍시다.
      	 * 여러 변환 방법이 있지만, 
      	 * JavaScript 작성의 컨벤션으로 많이 사용되는 AirBnb의 숫자 변환은 Number()를 사용해주는 것입니다. 
      	 * 여기서도 그렇게 해주도록 할게요. 
      	 */
        policyInfo[policyName] = Number(month)
    })
    
    privacies.map((infos, idx) => {
        const [dateInfo, policy] = infos.split(" ");
      	// 유효 완료 일자를 환산해준 값. 만으로 개월 수가 유효하기 때문에 1을 빼줍니다.
        const dateConverted = convertDate(dateInfo) + policyInfo[policy] * 28 - 1;
        
      	// 만약 오늘이 유효 일자를 넘었다면 파기해야 할 정보이므로 결과에 넣어줍니다.
        if (_today > dateConverted) {
          	// 인덱스가 1부터 시작하니까 잊지 말고 1 더해주기.
            result.push(idx + 1);
        }
    })
    
    return result;
}
```

---

<br>
