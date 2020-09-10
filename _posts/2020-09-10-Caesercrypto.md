---
layout : post
title : "시저 암호"
catrgories : ["Algorithm"]
---

## 문자와 문자열
&nbsp;&nbsp;컴퓨터에서 '문자'는 그 문자에 대응되는 정수'값'이다. 가령 'A'는 정수 '0x41'에 대응된다. 이렇게, 문자와 값을 대응한 것을 '문자 인코딩'이라 하고, 대표적인 인코딩으로 아스키(ASCII) 코드가 있다. 

&nbsp;&nbsp;컴퓨터에서의 '문자'는 알파벳과 숫자만 포함하지 않는다. 공백, 줄바꿈과 같은 '행위'와 관련된 것과, 컴퓨터에서 필요로 하는 특수한 값들(ex. End of File) 역시 문자로 간주한다. 가령 줄바꿈 행위는 '\n (Newline)'으로 나타내고, 공백은 ' ' 이라는 문자로 대응되어 있다.  

&nbsp;&nbsp;문자열은 복수의 문자들이 연속하여 존재하는 배열이다. 그런데 컴퓨터에서 문자는 정수형 값이다. 따라서 문자열은 복수의 정수형이 연속하여 존재하는 배열로 볼 수 있다.

&nbsp;&nbsp; C++ 언어에서는 ```std::string```객체를 통하여 문자열 객체를 지원한다.  ```std::string```은 문자열을 일반적인 연산자를 이용하여 다룰 수 있다. 
```
#include <iostream>
#include <string> // std::string은 string 헤더에 있음.

int main(){
    std::string example = "Hello";

    // example에 저장된 문자열을 모두 출력.
    std::cout << example << std::endl;
    
    /* Concatnation operator(+=): "Hello" + " World" = "Hello World"
     * 이 연산은 좌변의 문자열에 우변의 문자열을 복사하여 이어붙임.
     */
    
    example += " World" 
    
    // Relational Operator : 사전 순으로 확인 (strcmp와 원리가 유사함.)
    bool a = example >= string("Hey!")
    
    std::cout << example << std::endl;
    }
```

출력 결과는 아래와 같다. 
```
Hello
Hello World
```

## 해제와 풀이
### 해제
 &nbsp;&nbsp;이 문제는 문자열의 특성을 이용하여 시저 암호를 구현하는 것이다. 시저 암호은 알파벳을 순서대로 정렬한 후 특정 거리만큼 이동하여 암호화한 것이다. 다만, 공백은 이동하지 않고, 'Z' 를 1씩 이동하면 'A'가 된다. (소문자에 대해서도 동일하다.)
  

### 풀이
&nbsp;&nbsp;문제를 풀기 전에 요구사항을 살핀다. 

1. 'Z'를 1만큼 이동하면 'A'이고, 'z'를 1만큼 이동해도 'a'인 것에 대하여 : 기준문자를 정하고 Offset을 구한다.
    
    &nbsp;&nbsp;알파벳은 ASCII 코드에서 순서대로 값이 배정되어 있다. 따라서 가장 첫번째 문자인 'A'를 제외한 다른 문자들은 모두 'A'에 대한 Offset으로 나타낼 수 있다. (소문자의 경우에도 동일하다.)

    &nbsp;&nbsp;컴퓨터에서의 '문자'는 '정수형 값'이기 때문에, Offset은 문자들의 차로 구할 수 있다. 가령, 'Z'는 'A'로부터 25만큼 떨어져 있다. ASCII 코드에 따르면, ```'Z'=0x60, 'A'=0x41``` 이므로, ```'Z'-'A' = 0x60-0x41 = 0x19 = 25(decimal)``` 이기 때문이다.

    &nbsp;&nbsp;특정 문자에서 정수 n만큼 이동하는 것은 문자에 정수 n을 더하면 된다. 만약, 'C'에서 4만큼 이동하면 ```'C' + 4(decimal) = 0x43 + 0x4 = 0x47 = 'G'```가 된다. 

    &nbsp;&nbsp; 위 사례를 offset 관점에서 보면 'C'는 'A'로부터 2만큼 떨어져 있는 것이고, 'C'에서 4만큼 이동하면, 'A로부터 6만큼 떨어져 있는 문자 'G'에 대응하는 것이다. 

    &nbsp;&nbsp; 'Z'에서 1만큼 이동하면 'A'로부터 25만큼 떨어진 곳에서 1만큼 이동한 것이 되기 때문에, 'A'로부터 26만큼 떨어진 것이 된다.

    &nbsp;&nbsp; 'A'는 'A'에서의 거리가 0이고,  'z'는 4만큼 이동하면 'd'가 되어야 한다. 이 경우는 'a'로부터 29만큼 떨어진 것은 'd'이고, 'd'는 'a'로부터 3만큼 떨어진 것과 같아야 한다. 

    &nbsp;&nbsp; 따라서 Offset 26은 0에 대응하고, 29는 3에 대응한다. 그러므로 기존 Offset을 계산한 후 n을 더하고 26으로 나눈 나머지를 새로운 Offset으로 취한다.
    
2. 공백은 아무리 밀어도 공백이다 : 공백은 공백으로 처리하도록 한다.

3. 함수 solve의 argument(string s, int n) : n은 char형으로 변환한다.
    
    &nbsp;&nbsp;```std::string``` 객체의 내용물은 char로 저장하므로 n의 자료형을 char로 변환하여 이용하도록 한다. 

&nbsp;&nbsp;위 요구사항에 따라 시저암호를 구현하는 함수 shifting을 구현한다. 입력은 상수 문자 x와 밀고자 하는 값 n이다. 출력은 shifting이 된 결과를 char로 반환한다. 

```
char shifting(const char x, const int n) const {
    // 문자 x가 대문자인지, 소문자인지, 공백인지에 따른 기준문자 지정
    char radix = ' '; // 기준문자(radix) : 기본값은 공백.
    char res = ' ';   // Shifting 결과 : 기본값은 공백.
    if ( x >= 'A' &&  x <= 'Z' ) {
        // x가 대문자이면 기준문자는 'A'
        radix = 'A';
        }
    else if ( x >= 'a' && x <= 'z'){
        // x가 소문자이면 기준문자는 'a'
        radix = 'a';
        }
    // 만일 입력문자가 공백이 아니면, Offset을 이용한 Shifting 시행
    if ( x != ' ' ) {
        // offset 계산
        char offset = x-radix;
        // shifting 
        offset = (offset + static_cast<char>(n)) % 26;
        res = radix+offset;
        }
    return res; // 입력문자가 공백이면 공백 반환.
    } 
```

&nbsp;&nbsp;이를 이용하여 solve 함수는 다음과 같이 작성하였다. 

```
string solve(string s, int n){
    string answer = "";
    for ( char& _ch : s ) answer += shifting(_ch,n); 
    return answer;
    }
```

&nbsp;&nbsp;만일 solve 함수만 작성할 수 있다면, shifting 함수는 solve 내에 람다 함수로 구현하면 된다.


## 성공인증 
![Success](/img/1ok.png)
