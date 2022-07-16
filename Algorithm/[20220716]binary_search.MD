# Binary Search

학습 목표:
1. 이분 탐색의 개념에 대해서 이해한다.
2. 이분 탐색을 사용하여 백준 문제를 풀 수 있다.


## 개념
- 이분 탐색은 범위를 반으로 줄여나가며 검색 범위를 줄여나가는 방식이다.
- 데이터가 N개라면 데이터를 찾는 시간 복잡도는 O(log2(N))으로 데이터를 빠르게 찾을 수 있다.

## 특징 
- 이분 탐색을 하기 위해서는 데이터가 정렬되어 있어야 한다. 


## 이분 탐색 예시

- 데이터는 다음과 같다.
l = [2, 4, 7, 10, 20, 30, 50, 51, 53, 60, 80, 99, 100, 100, 100, 100, 100, 100, 100, 100, 100, 100, 100, 100, 100, 100,
     100]
     (27개)

- 몇 번째 인덱스에 53이 있는가?

```python
#
l = [2, 4, 7, 10, 20, 30, 50, 51, 53, 60, 80, 99, 100, 100, 100, 100, 100, 100, 100, 100, 100, 100, 100, 100, 100, 100,
     100]
find_num = 53

start = 0
end = len(l)
while start <= end:
    middle = (start + end) // 2
    print(f'start:{start} end:{end}, middle:{middle}')
    # 선택한 값이 찾을 숫자보다 큰 경우
    if l[middle] > find_num:
        end = middle - 1
    elif l[middle] < find_num:
        start = middle + 1
    else:
        print(f"53 is l[{middle}]")
        break

```

result
```shell

start:0 end:27, middle:13
start:0 end:12, middle:6
start:7 end:12, middle:9
start:7 end:8, middle:7
start:8 end:8, middle:8
53 is l[8]
```
