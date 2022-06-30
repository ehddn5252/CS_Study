# Quick Sort

목적: Quick Sort를 구현할 수 있다. Quick Sort의 정렬 방식에 대해서 설명할 수 있다.

## 코드
https://www.daleseo.com/sort-quick/ 

여기에서 가져온 코드인데, 코드가 매우 직관적이어서 코드만 봐도 설명이 되어서 맨 앞에 들고 왔다.

아래 코드를 보면 피봇보다 작으면 lesser 배열에 크면 greater 배열에 넣고 그걸 계속 반복한다.
```python
def quick_sort(arr):
    if len(arr) <= 1: # array가 0 또는 1이면 return
        return arr
    pivot = arr[len(arr) // 2] # 여기에서 피봇은 중앙 값으로 설정한다. 인덱스는 어떤 값으로 설정해도 상관 없다.
    lesser_arr, equal_arr, greater_arr = [], [], [] 
    for num in arr:
        if num < pivot: # 만약 값이 피봇보다 작다면 lesser arr에 넣고
            lesser_arr.append(num)
        elif num > pivot: # 만약 값이 피봇보다 크다면 greater_arr에 넣는다.
            greater_arr.append(num)
        else:
            equal_arr.append(num) # 만약 값이 같다면 equal_arr에 둔다.
    return quick_sort(lesser_arr) + equal_arr + quick_sort(greater_arr) # lesser_arr와 greater_arr 를 다시 quick sort해서 반복 정렬한다.
```

## 알고리즘 동작 설명
![image](https://user-images.githubusercontent.com/51036842/176369557-163f3176-87a6-49b4-abc3-171e90656fc1.png)
(출처: https://www.youtube.com/watch?v=7BDzle2n47c 동영상 설명)
1. 피벗을 잡는다.
2. 피벗을 가준으로 작은 값은 피봇 왼쪽에 피봇보다 큰 값은 오른쪽에 피봇 오른쪽에 둔다.
3. 2번 과정으로 피봇보다 작은 값은 모두 왼쪽 피봇보다 큰 값은 모두 오른쪽으로 이동 완료했을 경우 피봇을 기준으로 왼쪽 배열과 오른쪽 배열을 가지고 각각 1번 2번 과정을 반복한다.(재귀)

## 추가 내용
### 정의
- Qucik Sort 는 기준 키를 기준으로 작거나 같은 값을 지닌 데이터는 앞으로, 큰 값을 지닌 데이터는 뒤로 가도록 하여 적은 값을 갖는 데이터와 큰 값을 갖는 데이터로 분리해가며 정렬하는 방법이다.

### 시간복잡도
- 시간복잡도는 최적 O(NlogN), 평균 O(NlogN)이고 최악의 경우 O(N^2) 의 시간 복잡도를 가집니다.
- 최악의 경우는 배열이 오름차순으로 정렬되어 있거나 내림 차순으로 정렬되어 있는 경우입니다.

### 공간복잡도
- 주어진 배열 내에서 교환을 통해 정렬이 수행되므로 O(N)이다


### 추천 동영상
- 유튜버 코딩하는 거니님이 찍은 퀵소트 설명인데, 그림으로 이해하기 쉽게 설명해준다.
https://www.youtube.com/watch?v=cWH49IKDIiI
- 유튜버 엔지니어 대한민국 님이 찍은 퀵소트 설명인데, 그림이 더 직관적이다.
https://www.youtube.com/watch?v=7BDzle2n47c



### 참고 자료
- https://www.youtube.com/watch?v=cWH49IKDIiI
- https://terms.naver.com/entry.naver?docId=2270444&cid=51173&categoryId=51173
- https://www.daleseo.com/sort-quick/ 
- https://www.youtube.com/watch?v=7BDzle2n47c