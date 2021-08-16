# 병합 정렬 Merge Sort

- 재귀용법을 활용한 정렬 알고리즘

1.리스트를 절반으로 잘라서 비슷한 크기의 두 부분 리스트로 나누기

2.각 부분 리스트를 재귀적으로 합병 정렬을 이용해서 정렬

3.두 부분 리스트를 다시 하나의 정렬된 리스트로 합병

![https://upload.wikimedia.org/wikipedia/commons/c/cc/Merge-sort-example-300px.gif](https://upload.wikimedia.org/wikipedia/commons/c/cc/Merge-sort-example-300px.gif)

source: [https://en.wikipedia.org/wiki/Merge_sort](https://en.wikipedia.org/wiki/Merge_sort)

Merge sort는 크게 "Split"단계와 "Merge"단계로 구성되는데,

1.먼저 최대한으로 쪼개고,(정렬되지 않은 배열을 끝까지 분리)

2. 그 쪼개어진 것을 단계별로 병합해나간다!

그런데, 합칠 때에는, 한개씩 비교하면서 작은 값이 앞으로 가게 해서, 마치 포인터가 있는 것처럼 합쳐진다

차근차근 접근해보자

🌹 먼저 배열을 앞 뒤 두 배열로 잘라보자

나는 System.arraycopy를 이용해서 배열복사로 접근해주었다

```java
package sorting.merge;

import java.util.Arrays;

public class Test {

    public static Object[] splitFunc(Integer[] arr){
        int size= arr.length;

        if(size<=1){
            return null;
        }
        int mid = size/2;
        Integer[] split1=new Integer[mid];
        Integer[] split2=new Integer[mid];

        Object[] res = new Object[2];

        System.arraycopy(arr,0,split1,0,mid);
        System.arraycopy(arr,mid,split2,0,mid);

        res[0] = split1;
        res[1] = split2;

        return res;
    }

    public static void main(String[] args){
        //배열을 앞뒤  두 배열로 자르는 코드 작성하기
        Integer[] arr  = {3,5,2,4,6,7,8,1,9,10};

        Object[] splited= splitFunc(arr);

        System.out.println("splited#1: "+Arrays.toString((Integer[])splited[0]));
        System.out.println("splited#2: "+Arrays.toString((Integer[])splited[1]));

    }
}
```

절반 정도 위치 인덱스를 이용해서 

- 첫번째 배열은 0부터 절반인덱스 전까지 복사
- 두번째 배열은 절반인덱스부터 끝까지 복사

그리고 두 배열을 리턴하기 위해서 Object[] 배열을 이용했다

또 다른 방법은 ArrayList를 이용하고, 이 중 subList를 이용하는 방법이다!

```java
package sorting.merge;

import java.util.ArrayList;
import java.util.Arrays;

public class Test {

    public static ArrayList<ArrayList<Integer>> splitFunc2(ArrayList<Integer> arr){
        int size = arr.size();

        if(size<=1){
            return null;
        }

        int mid = size/2;
        //sublist- [0,mid)
        ArrayList<Integer> splited1= new ArrayList<>(arr.subList(0,mid));
        //[mid,size)
        ArrayList<Integer> splited2=new ArrayList<>(arr.subList(mid,size));

        ArrayList<ArrayList<Integer>> res = new ArrayList<>();
        res.add(splited1);
        res.add(splited2);

        return res;

    }

    public static void main(String[] args){
        //배열을 앞뒤  두 배열로 자르는 코드 작성하기
      
				ArrayList<ArrayList<Integer>> res = splitFunc2(new ArrayList<Integer>(Arrays.asList(3,5,2,4,6,7,8,1,9,10)));

        ArrayList<Integer> sp1=res.get(0);
        ArrayList<Integer> sp2=res.get(1);

        System.out.println("splited##1: "+sp1);
        System.out.println("splited##2: "+sp2);
    }
}
```

🌹이번에는 쪼개진 배열을 합치는 것을 연습해보자

- 이때에는 반복적으로 작업이 일어나는데, 자기자신을 호출하도록 재귀용법을 이용하자

📌 분리된 배열을 합치는 과정 규칙 📌

1. 만약, 배열 갯수가 1개이면 해당 값 리턴
2. 그렇지 않으면, 배열을 앞 뒤 두개로 나누어 합치기 시작
-leftArr = mergeSplitFunc(앞)
-rightArr= mergeSplitFunc(뒤)
-그런데 이 과정이 반복적이기 때문에 `재귀방식으로 접근` 해서 해결해보도록 하자! **[이렇게 1과 2로 나누는 것이 `divide(분할)` 이고! 위의 left,right로 계속 분할해나가는 것이 `conquer(정복)` 이라 할 수 있고, 이를 기반으로 합쳐나가면서 해결하는 과정이 `combine(결합)` 이라 할 수 있다!]**
3. return mergeFunc(leftArr, rightArr)[앞 뒤 배열을 합치기]라는 아직 정의되지 않은 메서드를 호출하여 리턴하기

🌟 단계 확장! - `mergeSplitFunc` 🌟

1) 만약 `배열의 갯수가 한개이면(벌써부터 재귀의 냄새가 팍팍!)` 해당 값을 리턴

2) `그렇지 않으면 배열을 앞 뒤 두개로 자르기

- leftArr = mergeSplitFunc(앞)
- rightArr= mergeSplitFunc(뒤)
- 결과적으로 leftArr내부에서도 rightArr내부에서도 다시금 나눠지기 때문에 자기가 인자값으로 또 들어가서 `재귀적으로` 반복하게 될 것!
- return mergeFunc(leftArr, rightArr)이라는 아직 정의되지 않은 메서드를 호출하여 리턴하기(이때, leftArr와 rightArr은 이미 정렬된 배열)

```java
package sorting.merge;

import java.util.ArrayList;

public class MergeSort {

    public ArrayList<Integer> mergeSplitFunc(ArrayList<Integer> list){
        int size=list.size();
        //인자값 길이가 1개 이하이면 그대로 반환
        if(size<=1){
            return list;
        }

        //배열을 앞뒤로 나누기
        int midIdx=size/2;
        ArrayList<Integer> leftArr = mergeSplitFunc(new ArrayList<>(list.subList(0,midIdx)));
        ArrayList<Integer> rightArr= mergeSplitFunc(new ArrayList<>(list.subList(midIdx,size)));

				System.out.println("midIdx: "+midIdx);
        System.out.println("left: "+leftArr);
        System.out.println("right: "+rightArr);
        //return mergeFunc(left,right)
        return mergeFunc(leftArr,rightArr);
    }

}
```

🌟 단계확장- `mergeFunc` 메서드 만들기 🌟

- leftArr과 rightArr의 배열 데이터를 정렬하면서 합쳐서, mergedArr이라는 이름으로 리턴
- leftArr과 rightArr은 이미 정렬된 상태 또는 데이터가 하나!!

- leftPointer와 rightPointer는 분할된 배열을 각각 왼쪽과 오른쪽으로 보았을 때, 각각의 선두를 나타내는 포인터!

0) ArrayList 만들기 ▶️ leftPointer, rightPointer=0

1) #CASE 1: leftArr과 rightArr이 모두 있을 때

- 배열크기=인덱스+1 이라는 점에서 아래와 같이 접근 가능!

```java
while(leftArr.size()>leftPointer && rightArr.size()>rightPointer)
```

- 왼쪽과 오른쪽 중 작은 부분을 먼저 병합된 배열에 추가하고, 해당 포인터를 1증가시키기!

```java
//오른쪽 배열의 값이 더 작은 경우, 병합된 배열의 앞에 왼쪽보다 오른쪽이 먼저
//오도록 추가하고, 오른쪽 포인터값을 증가!
if(leftArr.get(leftPointer) > rightArr.get(rightPointer){
	 mergedArr.add(rightArr.get(rightPointer));
	rightPointer++;
}else{
  //왼쪽배열의 값이 더 작은 경우, 왼쪽 배열의 값을 병합된 배열의 앞에
//오도록 추가하고, 왼쪽 포인터값을 증가!
	mergedArr.add(leftArr.get(leftPointer));
 leftPointer++;
}
```

2) #CASE2: RightArr만 없는 경우 : 나머지 leftArr에 있는 데이터를 그대로 mergedArr뒤에 넣기

3) #CASE3: LeftArr만 없는 경우 : 나머지 rightArr에 있는 데이터를 그대로 mergedArr뒤에 넣기

🌟 mergedArr에 들어갔다는 것은 해당 각각의 포인터 까지의 배열에 대해서는 정렬이 되었다는 것이 간주되었으므로, 그 이후 데이터에 대해서만 정렬하면 된다는 것을 의미!

🌟 case 2 혹은 case 3은 case 1 이후의 진행이므로, 둘 중 하나가 데이터가 없다는 것을 의미! ▶️ case 2 와 같은 경우, 왼쪽 데이터가 있다는 것과 같은 말인데, 만약 오른쪽 데이터도 있다면 그것은 case 1에 아직 남아있어야 하는 상황이기 때문에 모순적이다!!

```java
package sorting.merge;

import java.util.ArrayList;

public class MergeSort {

    public ArrayList<Integer> mergeSplitFunc(ArrayList<Integer> list){
        int size=list.size();
        //인자값 길이가 1개 이하이면 그대로 반환
        if(size<=1){
            return list;
        }

        //배열을 앞뒤로 나누기
        int midIdx=size/2;
        ArrayList<Integer> leftArr = mergeSplitFunc(new ArrayList<>(list.subList(0,midIdx)));
        ArrayList<Integer> rightArr= mergeSplitFunc(new ArrayList<>(list.subList(midIdx,size)));

				System.out.println("midIdx: "+midIdx);
        System.out.println("left: "+leftArr);
        System.out.println("right: "+rightArr);
        //return mergeFunc(left,right)
        return mergeFunc(leftArr,rightArr);
    }

    public ArrayList<Integer> mergeFunc(ArrayList<Integer> leftArr, ArrayList<Integer> rightArr) {
        //병합된 배열
        ArrayList<Integer> mergedArr=new ArrayList<>();

        //왼쪽과 오른쪽 부분 배열에 대한 포인터
        int leftPointer = 0;
        int rightPointer =0;

        int leftSize=leftArr.size();
        int rightSize=rightArr.size();
        //CASE#1: left,right모두 있을 때
        while(leftSize>leftPointer && rightSize>rightPointer){
             if(leftArr.get(leftPointer)>rightArr.get(rightPointer)){
                 mergedArr.add(rightArr.get(rightPointer));
                 rightPointer++;
             }else{
                 mergedArr.add(leftArr.get(leftPointer));
                 leftPointer++;
             }
            //배열 크기도 없데이트
            leftSize=leftArr.size();
            rightSize=rightArr.size();
        }

        //CASE#2 :right 데이터가 없을때
        while(leftSize>leftPointer){
            mergedArr.add(leftArr.get(leftPointer));
            leftPointer++;
            leftSize=leftArr.size();
        }
        //CASE#3: left 데이터가 없을 때
        while(rightSize>rightPointer){
            mergedArr.add(rightArr.get(rightPointer));
            rightPointer++;
            rightSize=rightArr.size();
        }
        System.out.println("now, list: "+mergedArr);
        System.out.println("===================================");
        return mergedArr;
    }

}
```

그리고 간단하게 어레이리스트를 이용해서 테스트해보도록 하자

```java
package sorting.merge;

import java.util.ArrayList;

public class Main {
    public static void main(String[] args){
        MergeSort ms = new MergeSort();
        ArrayList<Integer> list = new ArrayList<>();
        list.add(10);
        list.add(3);
        list.add(4);
        list.add(5);
        list.add(2);
        list.add(1);
        list.add(6);
        list.add(8);
        list.add(9);
        list.add(7);

        System.out.println("before : "+list);
        System.out.println("---");
        list = ms.mergeSplitFunc(list);
        System.out.println("---");
        System.out.println("after: "+list);
    }
}
```

```java
before : [10, 3, 4, 5, 2, 1, 6, 8, 9, 7]
---
midIdx: 1
left: [10]
right: [3]
now, list: [3, 10]
===================================
midIdx: 1
left: [5]
right: [2]
now, list: [2, 5]
===================================
midIdx: 1
left: [4]
right: [2, 5]
now, list: [2, 4, 5]
===================================
midIdx: 2
left: [3, 10]
right: [2, 4, 5]
now, list: [2, 3, 4, 5, 10]
===================================
midIdx: 1
left: [1]
right: [6]
now, list: [1, 6]
===================================
midIdx: 1
left: [9]
right: [7]
now, list: [7, 9]
===================================
midIdx: 1
left: [8]
right: [7, 9]
now, list: [7, 8, 9]
===================================
midIdx: 2
left: [1, 6]
right: [7, 8, 9]
now, list: [1, 6, 7, 8, 9]
===================================
midIdx: 5
left: [2, 3, 4, 5, 10]
right: [1, 6, 7, 8, 9]
now, list: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
===================================
---
after: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

Process finished with exit code 0
```

![https://github.com/hy6219/TIL/blob/main/Algorithm/Sort/Advanced/MergeSort/%EB%B3%91%ED%95%A9%EC%A0%95%EB%A0%AC.png?raw=true](https://github.com/hy6219/TIL/blob/main/Algorithm/Sort/Advanced/MergeSort/%EB%B3%91%ED%95%A9%EC%A0%95%EB%A0%AC.png?raw=true)

그러면, 콘솔에서 각 단계별 split, merge를 확인해볼 수 있는데, 이를 통해서 위와 같이 종이에 그린 후 정리해볼 수 있었다!

즉, 분할할 수 있는 최대한 분할해서 마치 원자 상태처럼 두고, 그 후에는 왼쪽과 오른족 파트끼리 각 케이스에 따라 합치게 되는 것을 확인해볼 수 있다

## 📌 알고리즘 분석 📌

(각 단계의 모든 데이터 갯수를 합해보면 결국 각각은 n개인 것을 볼 수 있다)

![https://github.com/hy6219/TIL/blob/main/Algorithm/Sort/Advanced/MergeSort/%EB%B3%91%ED%95%A9%EC%A0%95%EB%A0%AC%20%EC%8B%9C%EA%B0%84%EB%B3%B5%EC%9E%A1%EB%8F%84.png?raw=true](https://github.com/hy6219/TIL/blob/main/Algorithm/Sort/Advanced/MergeSort/%EB%B3%91%ED%95%A9%EC%A0%95%EB%A0%AC%20%EC%8B%9C%EA%B0%84%EB%B3%B5%EC%9E%A1%EB%8F%84.png?raw=true)

병합정렬은 `몇 단계 깊이까지 만들어지는 지를 depth` 라고 하고, i로 놓으면 (맨 위는 0)

각 단계의 깊이는 각 단계에서  $2^i$ 개의 노드가 발생되고,  $n/2^i$  로 표현 될 수 있다!

### 각 단계의 시간 복잡도

각 단계의 노드 안의 배열 데이터는 한 번씩은 체크되기 때문에 각각 $2^i * n/2^i$=O(n) 시간 복잡도를 갖는다!

### 만들어지는 총 단계 수에 따른 시간복잡도

각 단계 i 에서,

단계=i, 깊이=$n/2^i$,  노드 갯수=$2^i$ 이므로

$2^i$=n*깊이 ↔️ i = log(2)(n*const) ↔️ i = 단계 = log(2)n

따라서 단계는 항상 log(2)n 개 만큼 만들어진다! 따라서 단계 생성에 따른 시간 복잡도는 O(logn)

∴ 전체 시간 복잡도 = 모든 단계에 따른 시간복잡도 * 각 단계별 시간복잡도

=O(logn) * O(n) = O(nlogn)