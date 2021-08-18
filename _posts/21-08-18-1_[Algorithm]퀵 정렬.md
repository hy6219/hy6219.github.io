# 퀵 정렬

![https://upload.wikimedia.org/wikipedia/commons/9/9c/Quicksort-example.gif](https://upload.wikimedia.org/wikipedia/commons/9/9c/Quicksort-example.gif)

source: [https://upload.wikimedia.org/wikipedia/commons/9/9c/Quicksort-example.gif](https://upload.wikimedia.org/wikipedia/commons/9/9c/Quicksort-example.gif)

- `기준점(pivot)` 을 정해서, `기준점보다 작은 데이터는 왼쪽(left), 큰 데이터는 오른쪽(right)`으로 모으는 함수 작성[보통 맨 앞을 피벗으로 삼고 시작]
- 각 왼쪽(left), 오른쪽(right)은 재귀용법을 사용해서 다시 동일 함수를 호출하여 동일 작업을 반복
- 함수는 왼쪽(left) + 기준점(pivot)+ 오른쪽(right)를 리턴

🌟 왼쪽과 오른쪽은 정렬되지 않은 상태이다!

🌟 병합정렬은 끝까지 잘게 쪼개는데, 퀵정렬은 쪼개면서 어느정도 정렬을 해나가고 그 위치에 따라 순서대로 합치면 됨!

먼저, 아래의 리스트를 맨 앞 데이터를 기준으로 , 작은 데이터는 left 변수에 , 그렇지 않은 데이터는 right 변수에 넣어보자

dataList=[4,1,2,5,7]

```java
package sorting.quick;

import java.util.ArrayList;
import java.util.List;

public class Test {

    public static void splitFunc(List<Integer> list){
        int size=list.size();
        int pivot=list.get(0);
        List<Integer> left=new ArrayList<>();
        List<Integer> right=new ArrayList<>();

        if(size<=1){
            return;
        }

        for(int idx=1; idx < size; idx++){
            if(pivot<list.get(idx)){
                right.add(list.get(idx));
            }else{
                left.add(list.get(idx));
            }
        }
        System.out.println("left: "+left);
        System.out.println("right: "+right);
        System.out.println("pivot: "+pivot);

    }

    public static void main(String[] args){
        List<Integer> list= new ArrayList<>();
        list.add(4);
        list.add(1);
        list.add(2);
        list.add(5);
        list.add(7);

        splitFunc(list);
    }
}
```

위와 같이 생각을 정리해보면, 콘솔에서 다음과 같이 피벗보다 작으면 left에, 크면 right에 분리됨을 확인해볼 수 있다

```java
left: [1, 2]
right: [5, 7]
pivot: 4
```

참고로, 만약 아래처럼 int 배열에 대해서 arrayList를 만들어주고자 한다면,

new ArrayList<>(Arrays.asList(배열);

Integer를 사용하면 toString 등이 오버라이딩되어 사용하기에 편리할 수 있다

---

➕(확장)

dataList가 다음 세 데이터를 가지고 있을 때 맨 앞의 데이터를 기준으로

- 작은 데이터는 left 변수에 넣고
- 그렇지 않은 데이터는 right변수에 넣고
- left, right, pivot 변수에 들어있는 배열 아이템들을 하나의 배열로 정렬하여 출력해보기

Integer[] dataList={3,4,2}

```java
package sorting.quick;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class Test {

    public static void splitFunc(List<Integer> list){
        List<Integer> temp=new ArrayList<>();
        int size=list.size();
        int pivot=list.get(0);
        List<Integer> left=new ArrayList<>();
        List<Integer> right=new ArrayList<>();

        if(size<=1){
            return;
        }

        for(int idx=1; idx < size; idx++){
            if(pivot<list.get(idx)){
                right.add(list.get(idx));
            }else{
                left.add(list.get(idx));
            }
        }
        System.out.println("left: "+left);
        System.out.println("right: "+right);
        System.out.println("pivot: "+pivot);

        //2단계, 합치기
        left.forEach(i->{
            temp.add(i);
        });

        temp.add(pivot);

        right.forEach(i->{
            temp.add(i);
        });

        System.out.println("step2: merge left, pivot, right: "+temp);

    }

    public static void main(String[] args){
        List<Integer> list= new ArrayList<>(Arrays.asList(3,4,2));
        System.out.println("before: "+list);
        splitFunc(list);

    }
}
```

```java
before: [3, 4, 2]
left: [2]
right: [4]
pivot: 3
step2: merge left, pivot, right: [2, 3, 4]
```

위와 동일한 결과를 addAll(리스트)을 이용해서 변경해줄 수도 있다!

```java
package sorting.quick;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class Test {

    public static void splitFunc(List<Integer> list){
        List<Integer> temp=new ArrayList<>();
        int size=list.size();
        int pivot=list.get(0);
        List<Integer> left=new ArrayList<>();
        List<Integer> right=new ArrayList<>();

        if(size<=1){
            return;
        }

        for(int idx=1; idx < size; idx++){
            if(pivot<list.get(idx)){
                right.add(list.get(idx));
            }else{
                left.add(list.get(idx));
            }
        }
        System.out.println("left: "+left);
        System.out.println("right: "+right);
        System.out.println("pivot: "+pivot);

        //2단계, 합치기
        temp.addAll(left);
        temp.addAll(Arrays.asList(pivot));
        temp.addAll(right);

        System.out.println("step2: merge left, pivot, right: "+temp);

    }

    public static void main(String[] args){
        List<Integer> list= new ArrayList<>(Arrays.asList(3,4,2));
        System.out.println("before: "+list);
        splitFunc(list);

    }
}
```

---

이제 2단계 `합칠 때에 왼쪽과 오른쪽에 대해서 정렬이 필요하므로 재귀적으로 퀵소트를 수행하는 메서드를 부르도록 변경만 해주면 된다!

```groovy
package sorting.quick;

import java.util.ArrayList;
import java.util.Arrays;

public class QuickSort {

    public ArrayList<Integer> sort(ArrayList<Integer> list){
        ArrayList<Integer> merged=new ArrayList<>();
        int size= list.size();

        if(size<=1){
            return list;
        }
        int pivot =list.get(0);

        ArrayList<Integer> left= new ArrayList<>();
        ArrayList<Integer> right= new ArrayList<>();

        for(int i = 1; i < size; i++){
            if(list.get(i)>pivot){
                right.add(list.get(i));
            }else{
                left.add(list.get(i));
            }
        }

        **merged.addAll(sort(left));
        merged.addAll(Arrays.asList(pivot));
        merged.addAll(sort(right));**

        return merged;

    }

}
```

```groovy
package sorting.quick;

import java.util.ArrayList;
import java.util.Arrays;

public class Main {
    public static void main(String[] args){
        QuickSort qs=new QuickSort();
        ArrayList<Integer> arr=new ArrayList<>(Arrays.asList(3,2,5,1,6,4,8,10,9,7));
        System.out.println("Before: "+arr);
        arr=qs.sort(arr);
        System.out.println("After: "+arr);
    }
}
```

```groovy
Before: [3, 2, 5, 1, 6, 4, 8, 10, 9, 7]
After: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

그 결과, 정렬된 데이터를 확인해볼 수 있다!

### 📌  퀵 정렬의 시간복잡도 : `O(nlogn)`

- 단, 최악의 경우 모든 데이터를 비교하는 상황이 나와서 O(n^2)복잡도가 나올 수도 있다!