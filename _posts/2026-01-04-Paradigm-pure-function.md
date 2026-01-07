---
title: "명령형에서의 순수함수"
date: 2026-01-04 00:00:00 +0800
categories: [Paradigm]
tags: [pure function]
---

# 순수함수의 뜻와 예제

순수함수는 수학의 함수 개념을 일부 가져온 것이다. 컴퓨터는 물리적인 세계이므로 이상적인 세계에 있는 수학과 동일할 수는 없다. 수학과 다르게 컴퓨터는 메모리의 한계와 계산 속도의 한계가 있고 수의 범위가 있다. 또 오류를 처리해야 한다.

순수함수는 아래 두 가지 성질을 만족하는 함수이다.
* 동일한 인자에 대해 동일한 값을 리턴한다
* 부작용이 없다

프로그래밍에서 함수는 매개변수와 리턴값을 가진다.
외부에서 함수에 인자를 넣어서 부르면 함수 안에서는 매개변수에 들어온다.
동일한 인자에 대해 동일한 값을 리턴하려면 인자 외에 다른 값을 읽어서 계산에 사용하면 안된다.

인자 외의 읽을 수 있는 상태
* 함수 외부의 가변 공유 변수
* 입력 받기(키보드, 마우스 등)
* 외부에서 읽기(파일, 데이터베이스 등)
* 함수의 부름이 끝나도 계속 존재하는 함수 안의 변수로 다음 함수의 부름에서 사용 가능한 변수(C의 static 변수)

프로그래밍에서 함수의 부작용은 리턴값 외에 외부 상태에 영향을 미치는 것을 뜻한다.

부작용의 예
* 함수 외부의 가변 공유 변수의 값을 바꾸는 것(전역변수 등)
* 외부에 저장(파일, 데이터베이스 등)
* 출력하기(화면, 프린터, 네트워크 등)

```
function add: (a: integer, b: integer) -> (integer)
    return a + b
end

c: integer = add(3, 5)
display(c) // 8

```
3, 5는 인자이고 a, b는 매개변수이다. a의 값이 3, b의 값이 5가 된다.
위 함수는 두 성질을 다 만족하므로 순수함수이다.

```
function add: (a: integer, b: integer) -> (integer)
    x: integer = a + b
    display(x) // 8
    return x
end

c: integer = add(3, 5)
display(c) // 8

```
위 함수는 외부에 출력하는 부작용이 있는 비순수함수이다

```
count: integer = 0

function add: (a: integer, b: integer) -> (integer)
    x: integer = a + b
    count = count + 1
    return x
end

c: integer = add(3, 5)
display(c) // 8

```
위 함수는 가변 공유 변수의 값을 읽고 쓴다. 리턴값에는 영향을 미치지 않는다. 하지만 외부 가변 공유 변수에 값을 쓰므로 부작용이 있는 비순수함수이다.

```
m: integer = 2

function add: (a: integer, b: integer) -> (integer)
    return a + b + m
end

c: integer = add(3, 5)
display(c) // 10

m = 4
d: integer = add(3, 5)
display(d) // 12

```
위 함수에는 부작용은 없다. 하지만 가변 공유 변수의 값을 읽어서 계산에 사용하여 가변 공유 변수의 값이 외부에서 바뀌면 리턴값이 동일하지 않을 수 있다. 그러므로 비순수함수이다. 

```
m: integer = 2

function add: (a: integer, b: integer) -> (integer)
    x: integer = b + m
    return a + b
end

c: integer = add(3, 5)
display(c) // 8

m = 4
d: integer = add(3, 5)
display(d) // 8

```
위 함수에는 부작용이 없다. 가변 공유 변수의 값을 읽기는 하지만 리턴캆을 만들기 위한 계산에 사용하지 않는다. 그러므로 순수함수이다. 하지만 불필요한 동작이 있다.


# 순수함수의 다양한 구현

함수형은 함수의 구성과 조합으로 프로그램을 만드는 것이고 순수함수형은 함수형에 추가로 순수함수와 불변변수로 프로그램을 만드는 것이다.
순수함수와 불변변수는 순수함수의 인자가 불변인 것만 제외하고는 직접적인 관계가 없다. 순수함수형 프로그래밍 언어에서는 모든 변수를 불변으로 만들어 자동으로 모든 함수가 순수함수로 만든다.

```
function sum: (n: integer) -> (integer)
    return n + sum(n - 1)
end

```
재귀함수를 이용한 전통적인 함수형 순수함수 구현이다

```
function sum: (n: integer) -> (integer)
    s: integer = 0
    foreach i in [1, n]
        s = s + i
    end
    return s
end
```
명령형으로 순수함수를 구현한 것이다. 순수함수의 2가지 성질을 다 만족한다
불변 변수를 사용하지 않아도 순수함수를 만드는 데 문제가 없음을 알 수 있다

순수함수 안에서 명령형 코드를 써도 순수함수의 성질을 만족하면 된다는 생각을 가지고 더 깊이 파고들어 보자

```
function concatenate: (a: string, b: string) -> (string)

function concatenate: (array: string[], index: int) -> (string)
    if index = array.length
        return ""
    end
    return concatenate(array[index], concatenate(array, index + 1))
end

function concatenate: (array: string[]) -> (string)
    return concatenate(array, 0)
end
```
문자열을 합치는 순수함수이며, 순수함수형 방식으로 만들었다. 모든 변수가 불변이다.

```
function concatenate: (array: string[]) -> (string)
    stringBuffer: StringBuffer = StringBuffer()
    foreach element in array
        stringBuffer.Append(element)
    end
    return stringBuffer.ToString()
end

```
같은 작업을 하는 순수함수이며 명령형 방식으로 만들었다.
일반적으로 순수함수는 그 안에서 다른 함수를 부를 때 순수함수만을 불러야 순수성이 유지되는 것으로 알려져 있지만 위 예에서는 객체의 메소드를 사용했음에도 불구하고 순수성이 유지되었다.

순수함수에서 쓸 수 있는 클래스는 모든 필드가 private이고 public 메소드는 필드에 접근할 수 있다는 것만 빼고 순수함수와 같아야 한다.

정렬의 예를 보기로 하자.
```
function sort: (array: integer[]) -> (integer[])
```
이 형식이 순수함수형이다. 인자를 바꾸지 못하므로 새로 만든 정렬된 배열을 리턴한다.

```
function sort: (array: integer[])
```
이 형식이 명령형이다. 인자를 바꿀 수 있으므로 인자로 들어온 배열을 정렬한다.

두 가지 정렬 함수를 순수함수에서 다 사용할 수 있다. 

순수함수에서 쓸 수 있는 명령형 함수는 인자를 바꿀 수 있는 것만 제외하고 나머지는 순수함수와 같은 형태이다.

명령형 언어에서는 순수함수형이나 명령형 방식 둘 다 순수함수를 구현하는데 사용할 수 있다.
일반적으로는 순수함수형으로 구현하는 것이 권장되나 너무 복잡하거나 자주 사용하는데 메모리를 많이 사용하거나 너무 느리거나 하면 명령형으로 구현하는게 더 좋다.



```
function factorial: (n: integer) -> (integer)
    if n <= 1
        return 1
    end
    return n * factorial(n - 1)
end
```
재귀로 만든 순수함수이다.
factorial(n -1)의 리턴값을 n과 곱한다. 리넡하기 전 마지막 연산이 곱하기 연산이기 때문에 꼬리재귀가 적용되지 않는다.

```
function factorial: (n: integer, acc: integer) -> (integer)
    if n = 1
        return acc
    end
    return factorial(n - 1, acc * n)    
end

function factorial: (n: integer) -> (integer)
    return factorial(n, 1)
end
```
속도를 빠르게 하기 위해 꼬리 재귀를 사용한 순수함수이다.
하지만 꼬리 재귀를 사용하지 않는 재귀 버전보다 더 복잡하다.
재귀함수를 만들었을 때 자연스럽게 꼬리재귀가 만들어지는게 아니라면 꼬리 재귀를 의도적으로 만드는 것은 좋지 않다.
속도를 위해서라면 명령형으로, 그렇지 않다면 일반적인 재귀로 구현하는게 이해하기에 더 좋다.
이해하기 어렵더라도 꼬리재귀를 써야만 하는 경우는 순수함수형 언어에서 가변변수를 사용하지 못하는 경우 뿐이다.

```
function factorial: (n: integer) -> (integer)
    if n <= 1
        return 1
    end
    acc: integer = 1
    foreach i in [1, n]
        acc = acc * i
    end
    return acc
end
```

