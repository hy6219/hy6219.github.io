# 삽입 정렬 Insertion Sort

- 두 번째 인덱스부터 시작
- 해당 인덱스(키 값) 앞에 있는 데이터(B)부터 비교해서 키 값이 더 작으면 B값을 뒤 인덱스로 복사
- 위의 과정을 키 값이 더 큰 데이터를 만날 때까지 반복, 그리고 큰 데이터를 만난 위치 바로 뒤에 키 값을 이동

![https://upload.wikimedia.org/wikipedia/commons/9/9c/Insertion-sort-example.gif](https://upload.wikimedia.org/wikipedia/commons/9/9c/Insertion-sort-example.gif)

source: [https://commons.wikimedia.org/wiki/File:Insertion-sort-example.gif](https://commons.wikimedia.org/wiki/File:Insertion-sort-example.gif)

▶️ 앞에서부터 스캔해서 삽입해나가는 방식!

먼저 데이터가 4개인 경우만 연습해보자

[9,3,2,5]

- #1 : [**3**,9,2,5]
- #2 : [3,2,5,**9**]
- #3 : [**2**,3,5,9]

📌 알고리즘 구현 📌

1. for(int i=0; i < arr.length-1 ; i++)로 n-1번 반복
2. 반복문 안에서 for(int j = i+1; j > 0; j  - -)로 반복
-내부 반복문 안에서 arr[j] < arr[j-1]이면 스왑

[i가 증가될때마다, 그에 따라 i를 기준으로 두번째 요소를 시작으로 앞의 요소들과 비교]

이를 구현해보면 아래와 같은데, 이를 콘솔로 출력해보면

```java
package sorting.insertionSort;

import java.util.Arrays;

public class InsertionSort {
    public Integer[] insertionSort(Integer[] arr){
        int size= arr.length;

        System.out.println("original: "+Arrays.toString(arr));
        for(int i = 0 ; i < size-1; i++){

            for(int j = i+1; j>0; j--){
                int temp = 0;

                if(arr[j] < arr[j-1]){
                    temp = arr[j-1];
                    arr[j-1]=arr[j];
                    arr[j]=temp;
                    System.out.printf("swap#i: %d, j: %d\n",i,j);
                }else{
                    break;//순서가 알맞은 경우
                }
            }
            String format= String.format("#%d : %s",i, Arrays.toString(arr));
            System.out.println(format);
        }

        return arr;
    }
	}
```

```java
package sorting.insertionSort;

import java.util.Arrays;

public class InsertionSortTest {

    public static void main(String[] args){
        InsertionSort insertionSort = new InsertionSort();
        Integer[] arr = new Integer[10];

        for(int i = 0 ; i < 10; i++){
            arr[i]=(int)(Math.random()*100)+1;
        }

        arr= insertionSort.insertionSort(arr);
        System.out.println("[FIN]arr: "+ Arrays.toString(arr));
        /*
        * original: [71, 5, 35, 46, 52, 43, 86, 76, 39, 21]
swap#i: 0, j: 1
#0 : [5, 71, 35, 46, 52, 43, 86, 76, 39, 21]
swap#i: 1, j: 2
#1 : [5, 35, 71, 46, 52, 43, 86, 76, 39, 21]
swap#i: 2, j: 3
#2 : [5, 35, 46, 71, 52, 43, 86, 76, 39, 21]
swap#i: 3, j: 4
#3 : [5, 35, 46, 52, 71, 43, 86, 76, 39, 21]
swap#i: 4, j: 5
swap#i: 4, j: 4
swap#i: 4, j: 3
#4 : [5, 35, 43, 46, 52, 71, 86, 76, 39, 21]
#5 : [5, 35, 43, 46, 52, 71, 86, 76, 39, 21]
swap#i: 6, j: 7
#6 : [5, 35, 43, 46, 52, 71, 76, 86, 39, 21]
swap#i: 7, j: 8
swap#i: 7, j: 7
swap#i: 7, j: 6
swap#i: 7, j: 5
swap#i: 7, j: 4
swap#i: 7, j: 3
#7 : [5, 35, 39, 43, 46, 52, 71, 76, 86, 21]
swap#i: 8, j: 9
swap#i: 8, j: 8
swap#i: 8, j: 7
swap#i: 8, j: 6
swap#i: 8, j: 5
swap#i: 8, j: 4
swap#i: 8, j: 3
swap#i: 8, j: 2
#8 : [5, 21, 35, 39, 43, 46, 52, 71, 76, 86]
[FIN]arr: [5, 21, 35, 39, 43, 46, 52, 71, 76, 86]
        *
        * */

    }

}
```

original: [71, 5, 35, 46, 52, 43, 86, 76, 39, 21]
swap#i: 0, j: 1
#0 : [5, 71, 35, 46, 52, 43, 86, 76, 39, 21]
swap#i: 1, j: 2
#1 : [5, 35, 71, 46, 52, 43, 86, 76, 39, 21]
swap#i: 2, j: 3
#2 : [5, 35, 46, 71, 52, 43, 86, 76, 39, 21]
swap#i: 3, j: 4
#3 : [5, 35, 46, 52, 71, 43, 86, 76, 39, 21]
swap#i: 4, j: 5
swap#i: 4, j: 4
swap#i: 4, j: 3
#4 : [5, 35, 43, 46, 52, 71, 86, 76, 39, 21]
#5 : [5, 35, 43, 46, 52, 71, 86, 76, 39, 21]
swap#i: 6, j: 7
#6 : [5, 35, 43, 46, 52, 71, 76, 86, 39, 21]
swap#i: 7, j: 8
swap#i: 7, j: 7
swap#i: 7, j: 6
swap#i: 7, j: 5
swap#i: 7, j: 4
swap#i: 7, j: 3
#7 : [5, 35, 39, 43, 46, 52, 71, 76, 86, 21]
swap#i: 8, j: 9
swap#i: 8, j: 8
swap#i: 8, j: 7
swap#i: 8, j: 6
swap#i: 8, j: 5
swap#i: 8, j: 4
swap#i: 8, j: 3
swap#i: 8, j: 2
#8 : [5, 21, 35, 39, 43, 46, 52, 71, 76, 86]
[FIN]arr: [5, 21, 35, 39, 43, 46, 52, 71, 76, 86]

위와 같다! 이를 통해서 변화하는 i 에 따라 이를 기준으로 두번째 요소부터 시작해서, 그 앞의 요소들과 비교해서 정렬해나간다는 것을 이 과정을 통해 알아볼 수 있었다

📌 알고리즘 분석 📌

- 반복문이 2개 ▶️ $O(n^2)$
- 최악의 경우, $(n*(n-1))/2$
- 완전 정렬이 되어있는 상태에서는 최선은 O(n)