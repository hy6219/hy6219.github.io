# 해쉬테이블

- Key에 데이터(Value)를 매핑할 수 있는 데이터 구조
- 해쉬 함수를 통해서 배열에 키에 대한 데이터를 저장할 수 있는 주소(인덱스 번호)를 계산
- Key를 통해 바로 데이터가 저장되어 있는 주소를 알 수 있기 때문에 저장 및 탐색 속도가 획기적으로 빨라짐
- **미리** 해쉬 함수가 생성될 수 있는 주소에 대한 공간을 **배열로 할당** 후, 키에 따른 데이터 저장 및 탐색 지원

1. 해쉬 함수 Hash Function
- 임의의 데이터를 **고정된 길이의 값[해쉬(Hash), 해쉬 값(Hash Value), 해쉬 주소(Hash Address)]**으로 리턴해주는 함수

1. 해쉬 테이블 Hash Table
- 키값의 연산에 의해 직접 접근이 가능한 데이터 구조
- 슬롯 : 해쉬 테이블에서 한 개의 데이터를 저장할 수 있는 공간

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Algorithm/General/Data_Structure/HashTable/%ED%95%B4%EC%89%AC%ED%85%8C%EC%9D%B4%EB%B8%94.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Algorithm/General/Data_Structure/HashTable/%ED%95%B4%EC%89%AC%ED%85%8C%EC%9D%B4%EB%B8%94.png?raw=true)

위의 그림이 바로 해쉬함수와 해쉬테이블을 쉽게 이해할 수 있도록 해줄 것 같다!

Key-value로 된 리스트에서 키값을 이용해서 해쉬 함수가 어떤 해쉬/해쉬값(주소값)을 리턴해주면, 이를 이용해서 해쉬 테이블을 만든다. 이때 해쉬테이블 한 행이 바로 "Slot 슬롯"이 된다. 즉, 해쉬테이블은 슬롯들의 집합체인 것이다! 그리고 이 해쉬 테이블을 통해서 직접적인 접근이 가능하게 된다!(주소값으로 데이터 접근)

🌟 키값을 해쉬 함수에 넣으면 어디에 저장되어 있는지 확인할 수 있기 때문에, 모든 배열을 다 확인할 필요가 없음!

- 하지만 자바는 Slot에 대한 주소값을 저장하려면, Slot을 내부클래스로 만들고, 객체배열로 만들어서 관리해야 하기 때문에, 인스턴스 생성과 관련되어, 그 때마다 객체를 관리해주어야 하는 불편함은 존재!

📌 해쉬함수를 만드는 방법 중 한 가지- Division 기법

- 나누기를 통해 나머지 값을 사용하는 방법
- 슬롯의 갯수로 첫 번째 character값을 나눈 나머지값!

ex) 슬롯이 20개라고 가정

```java
String name= "John";
int hash = 0;

 hash=(int)(name.charAt(0))%20;
```

위의 Division 기법을 이용해서 간단하게 해쉬테이블 구조를 흉내내보자!

```java
package com.dataStructure.hashTable;

public class MyHash {
    //슬롯 기반 해쉬테이블
    public Slot[] hashTable;
    public int size;
    public MyHash(int size){
        this.size = size;
        this.hashTable = new Slot[size*31];
    }

    class Slot{
        //복잡도를 낮추기 위해서 String을 이용
        String value;

        Slot(String value){
            this.value=value;
        }
    }
    public void setSize(int size){
        this.size=size;
    }
    public int getSize(){
        return size;
    }
    //간단한 해쉬함수-오버라이딩한 것이 아님! 원래의 hashCode는 인자를 받지 않음!
    //division 방식 이용
    public int hashCode(String key){
        return (int)(key.charAt(0))%size;
    }
    //데이터 저장
    public boolean saveData(String key, String value){
        Integer address = this.hashCode(key);
        //이미 슬롯이 만들어진 적이 있다면 값만 바꿔주기
        if(this.hashTable[address]!=null){
            this.hashTable[address].value=value;
        }else{
            //슬롯이 없다면
            //슬롯 객체 생성
            this.hashTable[address] = new Slot(value);
        }
        return true;
    }

    //데이터 가져오기
    public String getData(String key){
        Integer address = this.hashCode(key);

        //해쉬값이 존재하는 경우
        if(this.hashTable[address]!=null){
            return this.hashTable[address].value;
        }else{
            //해쉬값이 존재하지 않은 경우 없으므로 return null
            return null;
        }
    }

}
```

먼저, 해쉬테이블은 슬롯을 기반으로 구성되어 있으므로 이들의 집합체이다!

1. 따라서, 먼저 Slot 객체의 배열 필드를 구성해준다
2. 그 후, size에 따른 처리가 division 기법이기 때문에 size에 대한 getter, setter도 나의 경우에는, 만약의 상황을 위해서 준비해주었다
3. 그리고, key-value를 저장하고 (saveData), key값으로 값을 가져오는(getData) 과정이 필요한데,
- 저장할 경우, 이미 존재했던 슬롯은 값만 교체해주고, 그게 아니라면 슬롯을 새로이 만들어준다
- 출력할 경우, 이미 해쉬값이 존재했다면 그 값을 반환해주고, 그게 아닌 경우에는 존재하지 않았다는 것이므로 null을 반환해준다

위의 절차에 의해 아래의 Main 클래스를 실행해주면 key값으로 value값을 단순 배열에서 찾는 것보다 쉽게 찾을 수 있다!

```java
package com.dataStructure.hashTable;

public class HashMain {
    public static void main(String[] args){

        MyHash hash = new MyHash(30);

        hash.saveData("John","010-1234-5678");
        hash.saveData("Chris","010-1234-5679");

        System.out.println("value of John:"+hash.getData("John"));//value of John:010-1234-5678
        System.out.println("value of Chris: " +hash.getData("Chris"));//value of Chris: 010-1234-5679

        System.out.println("hashCode of John: "+hash.hashCode("John"));//hashCode of John: 14
        System.out.println("hashCode of Chris: "+hash.hashCode("Chris"));//hashCode of Chris: 7

        System.out.println("===");
        System.out.println("re save of John & Chris");
        //값만 바뀔것
        hash.saveData("John","010-1234-1234");
        hash.saveData("Chris","010-1234-5555");

        System.out.println("value of John:"+hash.getData("John"));//value of John:010-1234-1234
        System.out.println("value of Chris: " +hash.getData("Chris"));//value of Chris: 010-1234-5555

        System.out.println("hashCode of John: "+hash.hashCode("John"));//hashCode of John: 14
        System.out.println("hashCode of Chris: "+hash.hashCode("Chris"));//hashCode of Chris: 7
    }
}
```

📌 해쉬테이블의 장단점

🌺 장점

- 데이터 저장/읽기 속도가 빠름(검색 속도가 빠름)
- 해쉬는 키에 대한 데이터가 있는지(중복) 확인이 쉬움

🌺 단점

- 일반적으로 저장곤간이 좀더 많이 필요
- 여러 키에 해당하는 주소가 동일할 경우, **충돌**을 해결하기 위한 별도 자료 구조가 필요

📌 해쉬 테이블의 주요 용도

- 검색이 많이 필요한 경우
- 저장, 삭제, 읽기가 빈번한 경우
- 캐쉬 구현시(중복확인이 쉬워서, 기존에 임시저장된 데이터가 있다면 이에 대한 대응이 빠르게 처리 가능)

📌 충돌 해결 알고리즘

해쉬충돌을 살펴보기 위해서

"C"로 시작하는 Cindy와 Chris에 대한 value를 division 기법을 이용한 위의 예를 도입해보면

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Algorithm/General/Data_Structure/HashTable/%ED%95%B4%EC%89%AC%EC%B6%A9%EB%8F%8C.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Algorithm/General/Data_Structure/HashTable/%ED%95%B4%EC%89%AC%EC%B6%A9%EB%8F%8C.PNG?raw=true)

위와 같이 Chris라는 키를 넣었을 때 Cindy에 매칭되는 값이 확인된다

그 이유는 지금 앞글자를 언급했던 것처럼, 지금 해쉬함수는 첫 번째 문자를 이용해서 주소값을 계산하기 때문에 해당 키에 대한 슬롯이 덮어써지기 때문이다!

이렇게 다양한 키가 동일한 주소를 가리키게 됨으로써 해당 키에 대한 value가 없어지는 현상이 나타나는 것을 "**해쉬충돌**"이라 한다!

1. Chaining 기법
- 개방 해슁 혹은 Open Hashing 기법 중 하나
- 해쉬 테이블 저장 공간 외의 공간을 활용하는 기법
- 충돌 발생시, **링크드리스트 자료구조를 이용해서 링크드 리스트로 데이터를 추가로 되에 연결시켜서 저장**하는 기법

```java
package com.dataStructure.hashTable;

public class HashChaining {
    //슬롯 기반 해쉬테이블
    public Slot[] hashTable;
    private int size;
    public HashChaining(int size){
        this.hashTable = new Slot[size];
        this.size=size;
    }

    class Slot{
        //복잡도를 낮추기 위해서 String을 이용
        //내 키에 해당되는 것인지, 링크드리스트에 연결되는 것인지 확인하는
        //절차가 필요
        String key;
        String value;
        //링크드리스트에서 포인터값이 필요
        Slot next;

        Slot(String key,String value){
            this.key = key;
            this.value=value;
            this.next=null;
        }
    }

    public int getSize() {
        return size;
    }

    public void setSize(int size) {
        this.size = size;
    }

    //간단한 해쉬함수-오버라이딩한 것이 아님! 원래의 hashCode는 인자를 받지 않음!
    //chaining 방식 이용
    public int hashCode(String key){
        return (int)(key.charAt(0))%size;
    }
    //데이터 저장
    public boolean saveData(String key, String value){
        Integer address = this.hashCode(key);
        //이미 슬롯이 만들어진 적이 있다면
        //링크드 리스트를 순회
        if(this.hashTable[address]!=null){
            //헤드부터 접근해서 링크드리스트로 이용해서 다음값 확인하기
            Slot findSlot = this.hashTable[address];
            Slot prevSlot = this.hashTable[address];

            while(findSlot!=null){
                if(findSlot.key==key){
                    //내가 찾는 키에 해당
                    findSlot.value = value;//이미 있는 슬롯 중 해당 슬롯의
                    //값만 업데이트
                    return true;
                }
                prevSlot=findSlot;
                findSlot=findSlot.next;

            }
            prevSlot.next=new Slot(key,value);//순회해도 키와 일치하지 않는 경우는
            //찾는 슬롯 이전 슬롯과 이어주기

        }else{
            //슬롯이 없다면
            //슬롯 객체 생성
            this.hashTable[address] = new Slot(key,value);
        }
        return true;
    }

    //데이터 가져오기
    public String getData(String key){
        Integer address = this.hashCode(key);

        //해쉬값이 존재하는 경우
        if(this.hashTable[address]!=null){
            Slot findSlot = this.hashTable[address];

            while(findSlot!=null){
                if(findSlot.key==key){
                    return findSlot.value;
                }
                findSlot = findSlot.next;
            }
            return null;//다 돌아도 없는 경우
        }else{
            //해쉬값이 존재하지 않은 경우 없으므로 return null
            return null;
        }
    }
}
```

Chaining기법을 이용하게 된다면 우선 관건은

"어떤 해쉬값이 같으면 " 키값이 동일한지 대조해보는데, 이때 링크드 리스트 구조에 기반한다

라는 점이다!

그렇기 때문에 

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Algorithm/General/Data_Structure/HashTable/Chaining%EA%B8%B0%EB%B2%95_1_key%EC%97%90%20%ED%95%B4%EB%8B%B9%EB%90%98%EB%8A%94%EC%A7%80%20%EB%A7%81%ED%81%AC%EB%93%9C%EB%A6%AC%EC%8A%A4%ED%8A%B8%EC%97%90%20%ED%95%B4%EB%8B%B9%EB%90%98%EB%8A%94%EC%A7%80%20%ED%99%95%EC%9D%B8%ED%95%98%EA%B8%B0.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Algorithm/General/Data_Structure/HashTable/Chaining%EA%B8%B0%EB%B2%95_1_key%EC%97%90%20%ED%95%B4%EB%8B%B9%EB%90%98%EB%8A%94%EC%A7%80%20%EB%A7%81%ED%81%AC%EB%93%9C%EB%A6%AC%EC%8A%A4%ED%8A%B8%EC%97%90%20%ED%95%B4%EB%8B%B9%EB%90%98%EB%8A%94%EC%A7%80%20%ED%99%95%EC%9D%B8%ED%95%98%EA%B8%B0.PNG?raw=true)

위와 같이 기존의 value값 뿐아니라, key값을 갖도록 Slot 클래스를 꾸미고

다음을 가리키는 존재가 필요하므로 Slot타입의 next라는 의미상 포인터를 필드로써 갖게 한다!

그리고 데이터를 저장할 경우,

🌹 슬롯이 존재하지 않으면

- 새로운 슬롯을 생성

🌹 슬롯이 존재하는 경우

- 키값으로 슬롯을 찾는데, 해쉬함수값이 같으면 링크드리스트로 연결되어 있으므로
- 해쉬함수의 결과값으로 해쉬테이블에 접근하고
- 그 결과값의 첫 번째 값을 기준으로 next를 통해서 다음값들을 모두 key값과 일치하는지 확인 후,
일치한다면 값만 업데이트해주고
- 그렇지 않으면 이전슬롯에 지금의 슬롯을 담고
- 다음슬롯으로 넘어간다
- 마지막으로, 일치하는 케이스가 없는 경우에는, 이전슬롯이 가장 마지막 상태일 것이기 때문에, 이전슬롯의 다음에 새로운 슬롯을 연결해준다

```java
public boolean saveData(String key, String value){
        Integer address = this.hashCode(key);
        //이미 슬롯이 만들어진 적이 있다면
        //링크드 리스트를 순회
        if(this.hashTable[address]!=null){
            //헤드부터 접근해서 링크드리스트로 이용해서 다음값 확인하기
            Slot findSlot = this.hashTable[address];
            Slot prevSlot = this.hashTable[address];

            while(findSlot!=null){
                if(findSlot.key==key){
                    //내가 찾는 키에 해당
                    findSlot.value = value;//이미 있는 슬롯 중 해당 슬롯의
                    //값만 업데이트
                    return true;
                }
                prevSlot=findSlot;
                findSlot=findSlot.next;

            }
            prevSlot.next=new Slot(key,value);//순회해도 키와 일치하지 않는 경우는
            //찾는 슬롯 이전 슬롯과 이어주기

        }else{
            //슬롯이 없다면
            //슬롯 객체 생성
            this.hashTable[address] = new Slot(key,value);
        }
        return true;
    } 
```

반대로, 데이터를 가져오려면,

🌹 해쉬값이 존재하지 않는다면, null을 반환하고

🌹 해쉬값이 존재한다면, 키값으로 받은 해쉬함수값을 기반으로 key값과 일치하는 경우, 그 슬롯의 값을 반환

하도록 한다!

```java
//데이터 가져오기
    public String getData(String key){
        Integer address = this.hashCode(key);

        //해쉬값이 존재하는 경우
        if(this.hashTable[address]!=null){
            Slot findSlot = this.hashTable[address];

            while(findSlot!=null){
                if(findSlot.key==key){
                    return findSlot.value;
                }
                findSlot = findSlot.next;
            }
            return null;//다 돌아도 없는 경우
        }else{
            //해쉬값이 존재하지 않은 경우 없으므로 return null
            return null;
        }
    }
```

이렇게 Chaining기법을 도입하면, 해쉬값은 같을지언정, 키에 대한 정확한 값을 가져오는 것을 확인해볼 수 있다

```java
package com.dataStructure.hashTable;

public class HashChainingTest {
    public static void main(String[] args){

        HashChaining hash = new HashChaining(30);

        hash.saveData("John","010-1234-5678");
        hash.saveData("Chris","010-1234-5679");
        hash.saveData("Cindy","010-1111-2222");

        System.out.println("value of John:"+hash.getData("John"));//value of John:010-1234-5678
        System.out.println("value of Chris: " +hash.getData("Chris"));//value of Chris: 010-1234-5679
        System.out.println("value of Cindy: "+hash.getData("Cindy"));//value of Cindy: 010-1111-2222

        System.out.println("hashCode of John: "+hash.hashCode("John"));//hashCode of John: 14
        System.out.println("hashCode of Chris: "+hash.hashCode("Chris"));//hashCode of Chris: 7
        System.out.println("hashCode of Cindy: "+hash.hashCode("Cindy"));//hashCode of Cindy: 7

    }
}
```

1. Linear Probing 기법
- 폐쇄 해슁 혹은 Closing Hashing 기법 중 하나
- 해쉬 테이블 저장공간 내에서 충돌 문제를 해결하는 기법
- 충돌 발생시, 해당 해쉬주소의 다음 주소부터 "**맨 처음 나오는 빈 공간"에 저장**하는 기법
- 저장 공간 활용도를 높이기 위한 기법

먼저, Linear Probing 기법은 접근하고자 하는 키가 저장되어 있다는 보장이 없기 때문에

Key값을 Value값 외의 필드로 가져야 한다

```java
class Slot{
        //복잡도를 낮추기 위해서 String을 이용
        //내 키가 저장되어있다는 보장은 없기 때문에
        //데이터가 해당 키에 매칭되는지 확인할 필요가 있음

        String key;
        String value;

        Slot(String key,String value){
            this.key = key;
            this.value=value;

        }
    }
```

그리고, 데이터를 저장할 때에는

🌹 슬롯이 존재하지 않는 경우

- key,value값을 갖는 슬롯을 생성

🌹 슬롯이 만들어진 적이 있다면

- 현재 입력으로 들어온 key값으로 접근한 해쉬테이블 위치의 key값이 key 인자값과 같은지 비교하여 동일하다면 해당 위치의 value값에 value 인자값 업데이트
- 그렇지 않다면 주소를 한칸씩 옮기다가, key값을 발견하면 value값 업데이트(다만, 마지막에 다다르면 return false로 처리하여 간단하게 생각하기)
- 만약 위의 두 경우를 진행한 후에도 return 된 경우가 없다면 새로운 슬롯을 만들기

```java
public boolean saveData(String key, String value){
        Integer address = this.hashCode(key);
        //이미 슬롯이 만들어진 적이 있다면
        if(this.hashTable[address]!=null){
          //현재 해쉬테이블의 주소값에 있는 키를 비교
            if(this.hashTable[address].key==key){
                this.hashTable[address].value=value;//값만 업데이트
                return true;
            }else{
                //한칸씩 뒤로 옮기기
                Integer curAddr = address + 1;
                while(this.hashTable[curAddr]!=null){
                    //빈공간을 찾아 나서서 옮길 것!
                    if(this.hashTable[curAddr].key==key){
                        this.hashTable[curAddr].value=value;//값 업데이트
                        return true;
                    }
                    //다음슬롯으로 이동
                    curAddr++;
                    //주소가 맨 끝일 경우 처리
                    if(curAddr >=this.hashTable.length){
                        return false;
                    }
                }
                //주소에 해당되는 슬롯이 없는 경우
                //새로운 슬롯을 만들어줌
                this.hashTable[curAddr]= new Slot(key,value);
                return true;
            }

        }else{
            //슬롯이 없다면
            //슬롯 객체 생성
            this.hashTable[address] = new Slot(key,value);
        }
        return true;
    }
```

데이터를 꺼내올 때에는 

🌹 슬롯이 비어있다면 null반환

🌹 슬롯이 비어있지 않다면

- 해쉬테이블 내의 주소값으로 가져온 키값과 key 인자값이 동일하면 해쉬테이블에서 해당되는 value 반환
- 위의 경우가 아니라면 한칸씩 넘어가면서 데이터를 확인

```java
//데이터 가져오기
    public String getData(String key){
        Integer address = this.hashCode(key);

        //해쉬값이 존재하는 경우
        if(this.hashTable[address]!=null){
            //내가 찾는 키인지 먼저 확인
            if(this.hashTable[address].key==key){
                return this.hashTable[address].value;
            }else{
                //다음슬롯으로 넘어가서 빈공간이 없는 한,
                //데이터 확인
                Integer curAddr = address+1;

                while(this.hashTable[curAddr]!=null){
                    if(this.hashTable[curAddr].key==key){
                        return this.hashTable[curAddr].value;
                    }else{
                        curAddr++;
                        if(curAddr >= this.hashTable.length){
                            return null;//끝까지 간 상황
                        }
                    }
                }
                //해당 데이터가 없는 경우
                return null;
            }
        }else{
            //해쉬값이 존재하지 않은 경우 없으므로 return null
            return null;
        }
    }
```

참고로, 위의 과정으로 완성된 코드는 아래와 같다

```java
package com.dataStructure.hashTable;

public class HashLinearProbing {
    //슬롯 기반 해쉬테이블
    public Slot[] hashTable;
    private int size;
    public HashLinearProbing(int size){
        this.hashTable = new Slot[size];
        this.size=size;
    }

    class Slot{
        //복잡도를 낮추기 위해서 String을 이용
        //내 키가 저장되어있다는 보장은 없기 때문에
        //데이터가 해당 키에 매칭되는지 확인할 필요가 있음

        String key;
        String value;

        Slot(String key,String value){
            this.key = key;
            this.value=value;

        }
    }

    public int getSize() {
        return size;
    }

    public void setSize(int size) {
        this.size = size;
    }

    //간단한 해쉬함수-오버라이딩한 것이 아님! 원래의 hashCode는 인자를 받지 않음!
    //chaining 방식 이용
    public int hashCode(String key){
        return (int)(key.charAt(0))%size;
    }
    //데이터 저장
    public boolean saveData(String key, String value){
        Integer address = this.hashCode(key);
        //이미 슬롯이 만들어진 적이 있다면
        if(this.hashTable[address]!=null){
          //현재 해쉬테이블의 주소값에 있는 키를 비교
            if(this.hashTable[address].key==key){
                this.hashTable[address].value=value;//값만 업데이트
                return true;
            }else{
                //한칸씩 뒤로 옮기기
                Integer curAddr = address + 1;
                while(this.hashTable[curAddr]!=null){
                    //빈공간을 찾아 나서서 옮길 것!
                    if(this.hashTable[curAddr].key==key){
                        this.hashTable[curAddr].value=value;//값 업데이트
                        return true;
                    }
                    //다음슬롯으로 이동
                    curAddr++;
                    //주소가 맨 끝일 경우 처리
                    if(curAddr >=this.hashTable.length){
                        return false;
                    }
                }
                //주소에 해당되는 슬롯이 없는 경우
                //새로운 슬롯을 만들어줌
                this.hashTable[curAddr]= new Slot(key,value);
                return true;
            }

        }else{
            //슬롯이 없다면
            //슬롯 객체 생성
            this.hashTable[address] = new Slot(key,value);
        }
        return true;
    }

    //데이터 가져오기
    public String getData(String key){
        Integer address = this.hashCode(key);

        //해쉬값이 존재하는 경우
        if(this.hashTable[address]!=null){
            //내가 찾는 키인지 먼저 확인
            if(this.hashTable[address].key==key){
                return this.hashTable[address].value;
            }else{
                //다음슬롯으로 넘어가서 빈공간이 없는 한,
                //데이터 확인
                Integer curAddr = address+1;

                while(this.hashTable[curAddr]!=null){
                    if(this.hashTable[curAddr].key==key){
                        return this.hashTable[curAddr].value;
                    }else{
                        curAddr++;
                        if(curAddr >= this.hashTable.length){
                            return null;//끝까지 간 상황
                        }
                    }
                }
                //해당 데이터가 없는 경우
                return null;
            }
        }else{
            //해쉬값이 존재하지 않은 경우 없으므로 return null
            return null;
        }
    }
}
```

이를 기반으로 위에서 테스트해본 몇 가지 데이터를 넣어주면

해쉬주소가 같더라도 충돌을 피하여 키에 대한 value값이 잘 저장되고 추출되는 것을 볼 수 있다

```java
package com.dataStructure.hashTable;

public class HashLinearProbingTest {
    public static void main(String[] args){

        HashLinearProbing hash = new HashLinearProbing(30);

        hash.saveData("John","010-1234-5678");
        hash.saveData("Chris","010-1234-5679");
        hash.saveData("Cindy","010-1111-2222");

        System.out.println("value of John:"+hash.getData("John"));//value of John:010-1234-5678
        System.out.println("value of Chris: " +hash.getData("Chris"));//value of Chris: 010-1234-5679
        System.out.println("value of Cindy: "+hash.getData("Cindy"));//value of Cindy: 010-1111-2222

        System.out.println("hashCode of John: "+hash.hashCode("John"));//hashCode of John: 14
        System.out.println("hashCode of Chris: "+hash.hashCode("Chris"));//hashCode of Chris: 7
        System.out.println("hashCode of Cindy: "+hash.hashCode("Cindy"));//hashCode of Cindy: 7

    }
}
```

하지만 위의 두 경우는 성능이 떨어지게 될 수 있기 때문에 "빈번한 충돌을 개선"할 수 있도록 하는 것이 필요하다!

🌺 빈번한 충돌을 개선하는 방법 🌺

1. 해쉬테이블 저장공간을 확대
2. 해쉬 함수를 재정의

가장 좋은 것은 해쉬테이블의 저장공간을 확대하는 것!

예) 기존의 20개에 대해서 다루는 것을 200개로 고려

```java
String name="DaveLee";
int    res =  (int)(name.charAt(0))%200;
```

- 하지만 저장공간이 확대된다고 해서, 각자 다른 데이터가 다른 공간에 저장될 리는 만무함!
- 이런 경우에는 저장공간도 확대하고 함수도 재정의하는 것이다!

그래서 나는 저번에 자바 hashCode 작성과 관련해서

Objects.hashCode(Object)도 있지만 31을 이용하면 중복되지 않게 처리할 수 있고(문자열에 대해서)

정수의 경우에는 값만 더하거나 혹은 정수는 중복 배제에 불필요하기 때문에 배제함이 좋다는 점을 떠올려서 아래와 같이 각 자리의 수에 31을 곱한 값을 더해주도록 해보았다!

```java
package com.dataStructure.hashTable;

public class OptimalHashTable {
    class Slot{
        String key;
        String value;
        Slot(String key, String value){
                this.key=key;
                this.value=value;
        }

    }
    public Slot[] hashTable;

    public int size;

    public OptimalHashTable(int size){
        this.size= Character.MAX_VALUE-1;
        this.hashTable=new Slot[this.size];
    }

    public void setSize(int size){
        this.size=Character.MAX_VALUE-1;
    }

    public int getSize(){
        return size;
    }

    //해쉬함수
    public int hashCode(String key){
        int hash= 0;
        for(int i = 0 ; i < key.length(); i++){
            hash+=31*key.charAt(i);
        }

        return hash;
    }

    //데이터 저장
    public boolean saveData(String key, String value){
        /*
        * 키값에 대한 해쉬함수 결과값을 이용해서
        * 존재했다면 값을 업데이트
        * 아니라면 새로 생성
        * */
        Integer address = this.hashCode(key);

        if(this.hashTable[address]!=null){
            this.hashTable[address].value=value;
        }else{
            this.hashTable[address]=new Slot(key,value);
        }
        return true;
    }
    //데이터 꺼내기
    public String getData(String key){
        Integer address = this.hashCode(key);

        if(this.hashTable[address]!=null){
            return this.hashTable[address].value;
        }else{
            return null;
        }
    }

}
```

자바에서는 HashMap에서 해쉬테이블이 구현되어 있으므로 참고하자!

```java
package com.dataStructure.hashTable;

import java.util.HashMap;

public class HashMapMain {
   public static void main(String[] args){
        HashMap<Integer, String> map= new HashMap<>();

        map.put(1,"사과");
        map.put(2,"바나나");
        map.put(3,"포도");

       map.entrySet().forEach(entrySet->{
           StringBuilder sb = new StringBuilder();

           sb.append("[key="+entrySet.getKey()+", value="+entrySet.getValue()+"]\n");
           System.out.print(sb);
       });
    /*
    * [key=1, value=사과]
[key=2, value=바나나]
[key=3, value=포도]
    *
    * */
       System.out.println(map.get(1));//사과-키값1로 value값을 가져옴
   }
}
```

hashmap은 컬렉션에서 공부하였듯, key-value로 묶여진 자료구조인데

위에서 설명한 점을 덧붙이면 해쉬테이블 구조를 이용한 구조이다!

📌 시간복잡도

- 일반적인 경우(Collision이 없는 경우)- O(1)
- 최악의 경우(Collision이 모두 발생하는 경우)-O(n)
- 해쉬테이블의 경우는 일반적인 경우를 기대하고 작성

ex) 검색에서 해쉬테이블의 사용 예

- 배열에 데이터를 저장하고 검색할 경우(모든 충돌이 발생하는 경우! ◀️ Collision 해결 알고리즘을 적용하지 않았을 때) - O(n)
- 이상적인 해쉬 테이블 케이스에서 데이터를 검색할 때 -O(1)