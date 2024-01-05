# Type Annotations

파이썬에서는 데이터 유형을 명시적으로 지정하지 않아도 코드가 실행될때 파이썬 인터프리터가 타입을 자동으로 추론하여 지정해준다.

때문에 협업하여 작어 변수 등의 타입 등을 명확히 알 수 없어 문제가 생길 수 있다.

이러한 문제를 해결하기 위해 파이썬에서는 타입에 대한 힌트(Type Hinting)을 제공하는 TypeAnnotaions을 제공합니다.



### 1. 변수에 Annotations 사용하기

* 아래와 같은 형식

```python
my_var: <type> = <value>
```

* 적용

```python
# before
name = "Kim"
age = 99
friends = ["kim","Lee","Park","Lim"]
is_male = True
abc = {
    'a':'A',
    'b':'B',
    'c':'C'
}
```

```python
# after
name: str = "Kim"
age: int = 99
friends: list = ["kim","Lee","Park","Lim"]
is_male: bool = True
abc: dict = {
    'a':'A',
    'b':'B',
    'c':'C'
}
```



### 2. 함수에 Annotations 사용하기

* 인자 타입과 반환 타입에 대해 타입 힌트를 제공 할 수 있다.
* 아래와 같은 형식

```python
def my_func(param: <type>) -> <output-type>:
  # 내용
    return # 반환
```



* 적용

<pre class="language-python"><code class="lang-python"><strong># after
</strong><strong>def plus(n1: int, n2: int) -> int:
</strong>	return n1 + n2
    
def plus_minus(n1: int, n2: int) -> list:
	return [n1 + n2, n1 - n2]

def print_message(msg: str) -> None:
	print(msg)
    
def mystery_combine(a: str, b: str, times: int) -> str:
    return (a + b) * times
</code></pre>

