---
layout : post
title : "이상한 문자 만들기"
catrgories : ["Algorithm"]
---

## 일러두기
  이 문제의 풀이 방법은 "시저 암호" 문제를 푸는 방법과 비슷합니다. 문자열과 문자에 관한 사항은 ["시저 암호"를 푸는 방법](http://www.hyeonsookim.me/articles/2020-09/Caesercrypto)을 참조하세요.

## 해제와 풀이
### 해제
 &nbsp;&nbsp;이 문제는 문자열의 특성을 이용하여 문제를 해결하는 것이다 대문자를 소문자로 바꾸는 것과 소문자를 대문자로 바꾸는 방법을 구현할 수 있는지를 알아보려는 문제로 추정된다. 
  

### 풀이
&nbsp;&nbsp;문제를 풀기 전에 요구사항을 살핀다. 

1. 공백을 기준으로 단어를 나누고, 단어별로 인덱싱해야 한다. : 공백을 만나면 인덱스를 초기화하도록 한다. 

2. 단어 내 첫 글자는 인덱스가 0이다.

3. 짝수번째 글자는 대문자로, 홀수번째 글자는 소문자로 바꾼다.
    
    &nbsp;&nbsp; 대문자를 소문자로 바꾸는 루틴과 소문자를 대문자로 바꾸는 루틴을 만든다. "시저 암호"와 같이 기준문자와 Offset을 이용한다. 가령, 'Z'는 'A'로부터 25만큼 떨어져 있는 글자이므로(ASCII 코드표상) 'z'는 'a'로부터 25만큼 떨어져있는 글자로 계산하면 된다. 

    &nbsp;&nbsp; ASCII 코드표에서는 영어 대문자 - 특수문자 - 영어소문자 순으로 코드가 정해져 있으므로, 알파벳인지 아닌지를 확인하는 루틴이 필요하다. (예외 확인)



&nbsp;&nbsp;위 사항에 따라 기능을 구현하면 아래와 같다. 

```
#include <vector>

using namespace std;

// 대/소문자 확인 Routine.
bool is_upper(const char& x) {
    return x >= 'A' && x <= 'Z';
}

bool is_lower(const char& x) {
    return x >= 'a' && x <= 'z';
}

// 소문자 -> 대문자.
void toUpper(char* target){
    if ( is_lower(*target) ) {
        int offset = *target -'a';
        *target = 'A'+offset;
    	} 
}
// 대문자 -> 소문자.
void toLower(char* target){
    if (is_upper(*target)) {
        int offset = *target -'A';
        *target = 'a'+offset;
    }
}
```

&nbsp;&nbsp;이를 이용하여 solution 함수는 다음과 같이 작성하였다. 

```

string solution(string s) {
    string answer = string(s); // 입력된 string s 복사
    size_t length = answer.length(); // size_t std::string::length(void)
    size_t idx = 0U; // 단어 내 인덱스 값.
	for ( size_t iter = 0; iter < length ; iter++){
        // 공백이면 인덱스(idx) 초기화.
        if ( answer[iter] == ' ' ) {idx = 0U ; continue;}
        else if ( idx % 2 == 1 ) toLower(&(answer[iter])); //인덱스가 짝수인 경우
        else if ( idx % 2 == 0 ) toUpper(&(answer[iter])); //인덱스가 홀수인 경우
        idx++;
    }
    return answer;
}
```


## 성공인증 
![Success](/img/2ok.png)
