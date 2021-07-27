# 선택 정렬 Selection Sort

- 다음과 같은 순서를 반복하며 정렬
1. 주어진 데이터 중 최솟값을 찾기
2. 해당 최솟값을 데이터 맨 앞에 위치한 값과 교체
3. 맨 앞 위치를 제외한 나머지 데이터를 동일 방법으로 반복

![https://i2.wp.com/algorithms.tutorialhorizon.com/files/2019/01/Selection-Sort-Gif.gif?ssl=1](https://i2.wp.com/algorithms.tutorialhorizon.com/files/2019/01/Selection-Sort-Gif.gif?ssl=1)

source: [https://i2.wp.com/algorithms.tutorialhorizon.com/files/2019/01/Selection-Sort-Gif.gif?ssl=1](https://i2.wp.com/algorithms.tutorialhorizon.com/files/2019/01/Selection-Sort-Gif.gif?ssl=1)

데이터가 3개일 때와 4개일 때를 각각 생각해보자

가. 데이터가 3개일 때

[9,1,7]

- #1 : [**1**,9,7]
- #2 :[1,**7**,9]

나. 데이터가 4개일 때

[9,3,2,1]

- #1 : [**1**,3,2,9]
- #2 : [1,**2**,3,9]

이를 통해서 대략적으로 최대 n-1번 반복해야 함을 알 수 있다

그러면 정리해보면

1. n-1번 반복 for(int i= 0; i < arr.length-1;i++)
2. 최솟값 인덱스 설정 min = i
3. 내부 반복문 안에서 i 이후부터 반복(최솟값을 배제하고 진행하기 위함)
for(int j = i+1; j < arr.length; j++)
- 내부 반복문에서 arr[min] > arr[j]이면 최솟값 위치 변경
(최솟값 업데이트)
4. arr[min]과 arr[i] 스왑 ▶️ 최솟값을 범위 내 맨 첫 위치로!

위의 규칙을 정리해보면 아래와 같이 생각해볼 수 있다!

```java
package sorting.selectionSort;

import java.util.Arrays;

public class SelectionSort {
    public Integer[] selectionSort(Integer[] arr){
        Integer[] res = arr.clone();
        int min = 0;
        int size = res.length;
        //n-1번 반복
        for(int i = 0 ; i < size-1; i++){
            int temp = 0;

            min = i;

            for(int j = i+1; j < size;j++){
                //min 인덱스값 찾기
                if(res[min]>res[j]){
                    min = j;
                }
            }

            //arr[min]<->arr[i]
            temp = res[i];
            res[i]=res[min];
            res[min]=temp;

            System.out.println("min: "+min);
            String format = String.format("#%d :%s",i,Arrays.toString(res));
            System.out.println(format);
        }

        return res;
    }

}
```

그리고 이를 확인해보면

```java
package sorting.selectionSort;

public class SelectionSortTest {
    public static void main(String[] args){
        SelectionSort selectionSort = new SelectionSort();
        Integer[] arr = new Integer[10];

        for(int i = 0 ; i < 10; i++){
            arr[i]=(int)(Math.random()*100)+1;
        }

        Integer[] sorted = selectionSort.selectionSort(arr);

    }
}
```

min: 8
#0 :[9, 32, 94, 81, 51, 88, 23, 47, 39, 80]
min: 6
#1 :[9, 23, 94, 81, 51, 88, 32, 47, 39, 80]
min: 6
#2 :[9, 23, 32, 81, 51, 88, 94, 47, 39, 80]
min: 8
#3 :[9, 23, 32, 39, 51, 88, 94, 47, 81, 80]
min: 7
#4 :[9, 23, 32, 39, 47, 88, 94, 51, 81, 80]
min: 7
#5 :[9, 23, 32, 39, 47, 51, 94, 88, 81, 80]
min: 9
#6 :[9, 23, 32, 39, 47, 51, 80, 88, 81, 94]
min: 8
#7 :[9, 23, 32, 39, 47, 51, 80, 81, 88, 94]
min: 8
#8 :[9, 23, 32, 39, 47, 51, 80, 81, 88, 94]

위와 같은 결과가 콘솔에 찍혀지게 되어, 알맞게 정렬됨을 확인해볼 수 있다!

🌟 알고리즘 분석 🌟

- 반복문이 2개 ▶️ $O(n^2)$ [빅오표기법에서는 최고차항만 상관쓰고, 그의 계수도 묵시됨]
- 실제로 상세하게 계산하면 ▶️ $(n*(n-1))/2$