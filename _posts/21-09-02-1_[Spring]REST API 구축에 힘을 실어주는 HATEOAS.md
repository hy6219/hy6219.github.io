# Spring HATEOAS & PageModel

스프링을 예제로 조금 더 익히기 위해서 `처음 배우는 스프링 부트2` 라는 책을 접하고 있던 중, 해당 개념을 짧게 정리해보고자 한다

## 01. What is `HATEOAS` ?

`Hypermedia As The Engine Of Application State`

`REST 아키텍쳐의 구성 요소`

서버가 클라이언트에게 하이퍼미디어(링크를 통해 공유된 자원)를 통해 정보를 동적으로 제공해주는 방법

![https://external-preview.redd.it/eZ3OGc3Iw8WDorc13ZAsQy4lG5cSjactxBaf5mTUprA.jpg?auto=webp&s=181eff01e9484182c88efb65cd6ec58600cbb685](https://external-preview.redd.it/eZ3OGc3Iw8WDorc13ZAsQy4lG5cSjactxBaf5mTUprA.jpg?auto=webp&s=181eff01e9484182c88efb65cd6ec58600cbb685)

source: [https://external-preview.redd.it/eZ3OGc3Iw8WDorc13ZAsQy4lG5cSjactxBaf5mTUprA.jpg?auto=webp&s=181eff01e9484182c88efb65cd6ec58600cbb685](https://external-preview.redd.it/eZ3OGc3Iw8WDorc13ZAsQy4lG5cSjactxBaf5mTUprA.jpg?auto=webp&s=181eff01e9484182c88efb65cd6ec58600cbb685)

- RESOURCE=HTTP 응답에 들어갈 사용자, 게시판 글, 이벤트 등
- LINK= 리소스에 접근할 수 있는 참조 연결← 참조 정보 REL + 하이퍼링크 HREF

## 02. 🌟`PagedModel`

기존의 `PagedResources`가 변경된 형태

- Pageable 자료구조의 응답 표현을 dto에 바인딩하기 위해 필요한 객체

[PagedModel (Spring HATEOAS 1.3.3 API)](https://docs.spring.io/spring-hateoas/docs/current/api/org/springframework/hateoas/PagedModel.html)

### 생성자

- PagedModel(Collection<T> content, PagedModel.PageMetadata metadata, Iterable<Link> links)

혹은

PagedModel(Collection<T> content, PagedModel.PageMetadata metadata, Link... links)

→ links는 생략 가능

1) pageable 객체로부터 getContent

2) PagedModel.PagedMetadata 전달

## 03. 🌟`PagedModel.PageMetadata`

- 페이징(pagination)의 설정정보(메타데이터)의 value object

[PagedModel.PageMetadata (Spring HATEOAS 1.3.3 API)](https://docs.spring.io/spring-hateoas/docs/current/api/org/springframework/hateoas/PagedModel.PageMetadata.html)

- boolean equals(Object obj) : 동등성 비교
- long getNumber() : 현재 페이지
- long getSize() : 한 페이지 크기
- long getTotalPages() : 전체 페이지 수
- long getTotalElements(): 전체  요소 수
- String toString()
- int hashCode()

### 생성자

- PageMetadata(long size, long number, long totalElements)

1) pageable객체로부터 반환되어야 할 요소들의 갯수를 받고(`한 페이지에 출력될 갯수 확인)`

2) pageable객체로부터 `현재 페이지(getNumber())`를 받고

3) `소유중인 전체 요소 수(getTotalElements()`) 를 받아서

생성자로 전달해준다

- PageMetadata(long size, long number, long totalElements, long totalPages)

위에다가 추가로 `getTotalPages()로 얻은 전체 페이지수를 전달`

---

### 03-1.Pageable에서 지원되는 메서드(03 getTotalElements() 메서드 제외)

- int getNumberOfElements() : 결과 데이터 수
- int getPageSize() : 반환되어야 할 요소들의 갯수 반환
- List<T> getContent() : 조회된 데이터
- Sort getSort() : 검색 시 사용된 정렬 정보
- boolean hasPreviousPage() : 이전 페이지의 존재 여부
- boolean hasNextPage() : 다음 페이지의 존재 여부
- boolean isLastPage(): 지금이 마지막 페이지인지 여부
- Pageable nextPageable() : 다음 페이지 객체
- Pageable previousPageable(): 이전 페이지 객체

```java
package com.community.spring_boot_community.controller;

import com.community.spring_boot_community.domain.Board;
import com.community.spring_boot_community.repository.BoardRepository;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.web.PageableDefault;
import org.springframework.hateoas.PagedModel;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import static org.springframework.hateoas.server.mvc.WebMvcLinkBuilder.linkTo;
import static org.springframework.hateoas.server.mvc.WebMvcLinkBuilder.methodOn;

@RestController
@RequestMapping("/api/boards")
public class BoardRestController {

    private BoardRepository boardRepository;

    public BoardRestController(BoardRepository boardRepository){
        this.boardRepository=boardRepository;
    }

    @GetMapping(produces= MediaType.APPLICATION_JSON_VALUE)
    public ResponseEntity<?> getBoards(@PageableDefault Pageable pageable){
        Page<Board> boards=boardRepository.findAll(pageable);
        PagedModel.PageMetadata pageMetadata=new PagedModel.PageMetadata(pageable.getPageSize(),
                boards.getNumber(),boards.getTotalElements());
        PagedModel<Board> resources=new PagedModel<>(boards.getContent(),pageMetadata);
        resources.add(linkTo(methodOn(BoardRestController.class).getBoards(pageable)).withSelfRel());
        return ResponseEntity.ok(resources);
        /*
        * PageResources->PagedModel
        * ⓐ @PageableDefault : 한 페이지에 보여질 게시글 갯수, 정렬 기준을 세워주기 위한 어노테이션
        * ⓑ PageMetadata:
        *
        * */
    }

}
```

## 04. `@PageableDefault`

- 한 페이지에 보여질 게시글 갯수, 정렬 기준을 세워주기 위한 어노테이션

## 05. HATEOAS 링크 예제

json 처럼 key-value 형식으로 적어주는데

- `_links` : 위에서 언급된 링크요소!
- `first` : 첫 페이지
- `self`: 자기 자신의 URL의 파라미터 정보
- `next` : 다음 페이지
- `last` : 마지막 페이지

→ 페이지 처리에 관련된 정보를 key-value 형식으로 추가

→ 추후 버전관리나 형식 변경에도 클라이언트에서는 로직 수정이 줄어듦

```java
"_links":{
  "first":{
    "href":"http://localhost:8089/api/boards?page=0&size=10"
  },
  "self":{
    "href":"~"
  },
  "next":{
    "href":"http://localhost:8089/api/boards?page=1&size=10"
  },
    "last":{
    "href":"http://localhost:8089/api/boards?page=20&size=10"
  }
}
```

🌟🌟 모듈화된 프로젝트는 우측 gradle의 `Tasks`- `application` - `bootRun` 을 눌러주면 된다!