# 자바 스프링 구조 및 컨벤션
작성일: 2022.08.28

목적: 자바 컨밴션 정리 및 Spring 폴더 구조 정리
### 목차
1. 코드 컨밴션
2. 주석 컨밴션

## 1. 코드 컨밴션
캠퍼스 핵데이 Java 코딩 컨벤션을 사용한다.
- [핵데이 컨밴션 공식 문서](https://naver.github.io/hackday-conventions-java/)

핵데이 컨밴션 요약(많이 쓰는 것만)
- 패키지(폴더)명은 소문자로 적는다.
- 클래스/인터페이스 이름에 대문자 카멜표기법 적용한다.
- 클래스 이름에 명사 사용한다.
- 메서드 이름에 소문자 카멜표기법 적용한다.
- 상수는 대문자와 언더스코어로 구성한다.
- 변수에 소문자 카멜표기법 적용한다.
- tab 문자를 사용해 들여쓴다.
- 파일 인코딩은 UTF-8 를 사용한다.
- 변수명, 클래스명, 메서드명 등에는 영어와 숫자만을 사용한다.
- 식별자의 이름을 한글 발음을 영어로 옮겨서 표기하지 않는다. 한국어 고유명사는 예외이다. -> 한글 -> 영어 뜻 검색해서 사용하기
- 조건/반복문에 중괄호 필수 사용 -> 한 줄로 끝나더라도 중괄호를 사용한다.

## 2. 주석 컨밴션
- 매 java 클래스 에는 작성자 및 class 설명을 기본으로 한다.
- method 주석에는 method 이름, 작성일, 작성자, method 설명 parameter, return 값 설명을 적는다.


### 클래스, 메소드 주석 예시

BoardServiceImpl.java
```java
package com.ssafy.api.service;

import ...;

/**

* @FileName : BoardServiceImpl.java
* @Date : 2022. 8. 28
* @작성자 : 김동우
* @변경이력 : x
* @프로그램 설명 : Board API에 필요한 서비스 구현체
*/
@Service("boardService")
public class BoardServiceImpl implements BoardService {
    @Autowired
    BoardRepository boardRepository;
    
    @Autowired
    BoardRepositorySupport boardRepositorySupport;

    /**
    * @Method Name : createBoard
    * @작성일 : 2022. 8. 28
    * @작성자 : 김동우
    * @변경이력 : x
    
    * @Method 설명 : client request를 받아 게시판을 하나 생성한다.
    * @param boardRegisterInfo: 사용자 board 작성 입력 정보 categoryLarge, categoryMiddle, content, img, title, userUid 의 변수가 들어 있다.
    * @return type Board: board에 사용자 작성 정보 + 현재 시간, 조회수를 DB board table에 저장한다.
    */
    @Override
    public Board createBoard(BoardRequest boardRegisterInfo) {
        
        Board board = new Board();
        
        board.setCategoryLarge(boardRegisterInfo.getCategoryLarge());
        board.setCategoryMiddle(boardRegisterInfo.getCategoryMiddle());
        board.setContent(boardRegisterInfo.getContent());
        board.setImg(boardRegisterInfo.getImg());
        board.setTitle(boardRegisterInfo.getTitle());
        board.setUserUid(boardRegisterInfo.getUserUid());
        Timestamp timestamp = new Timestamp(System.currentTimeMillis());
        board.setRegTime(timestamp);
        board.setViewCount(0);

        return boardRepository.save(board);
    }

```