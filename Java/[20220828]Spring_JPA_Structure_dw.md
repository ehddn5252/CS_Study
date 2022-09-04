# Spring JPA Structure
작성일: 2022.08.28

목적: Spring 폴더 구조 정리
### 목차
1. 폴더 구조

## 1. 폴더 구조
- ### entity folder
    - Database의 table과 매핑하는 데이터 전달 form 작성
    - Database의 table 이름과 동일하게 naming한다.
    <details>
    <summary span style="font-size:15px">entity 예시</summary>
    
    Board.java
    ```java
    package com.ssafy.db.entity;

    import java.io.Serializable;
    import java.util.Date;

    import javax.persistence.Column;
    import javax.persistence.Entity;
    import javax.persistence.GeneratedValue;
    import javax.persistence.GenerationType;
    import javax.persistence.Id;
    import javax.persistence.JoinColumn;
    import javax.persistence.ManyToOne;
    import javax.persistence.Table;

    import lombok.Getter;
    import lombok.Setter;


    @Entity
    @Getter
    @Setter
    @Table(name = "boards")
    public class Board extends BaseEntity implements Serializable {
        @Id
        @GeneratedValue(strategy = GenerationType.IDENTITY)
        int uid;


        /**
        * 1: 커뮤니티 2: 뉴스 3: 가이드
        */
        @Column(name = "category_large")
        int categoryLarge;

        /**
        * 1. 자유 게시글, 2. 소식, 3. 업데이트, 4. 댓글
        *  게임 소개, 게임 룰, 게임 모드
        */
        @Column(name = "category_middle")
        int categoryMiddle;

        String title;

        String content;

        @Column(name = "reg_time")
        Date regTime;

        @Column(name = "view_count")
        int viewCount;
        
        @Column(nullable = true, name = "img")
        String img;
        
        @Column(name="user_uid")
        int userUid;
        
        @ManyToOne()
        @JoinColumn(name="boardList")
        private User user;
    }
    ```
    </details>
- ### Controller 
    - 스웨거를 작성한다. 스웨거 내용으로는 함수 설명 및 parameter와 return 값을 적는다.
    - client 단에서 해당 uri로 보내는 request를 받고 그에 대한 response를 return 한다.
        - response로는 예외 페이지 처리를 해준다.
    - 직접 서비스를 구현하지 않는다.
    <details>
    <summary span style="font-size:15px">controller 예시</summary>

    CommunityController.java
    ```java
    package com.ssafy.api.controller;

    import java.util.List;

    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.http.HttpStatus;
    import org.springframework.http.ResponseEntity;
    import org.springframework.web.bind.annotation.DeleteMapping;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.PostMapping;
    import org.springframework.web.bind.annotation.PutMapping;
    import org.springframework.web.bind.annotation.RequestBody;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RequestParam;
    import org.springframework.web.bind.annotation.RestController;

    import com.ssafy.api.request.BoardRequest;
    import com.ssafy.api.response.BoardListResponse;
    import com.ssafy.api.response.BoardResponse;
    import com.ssafy.api.service.BoardService;
    import com.ssafy.api.service.UserService;
    import com.ssafy.common.model.response.BaseResponseBody;
    import com.ssafy.db.entity.Board;
    import com.ssafy.db.entity.User;
    import com.ssafy.infos.Authority;
    import com.ssafy.infos.BoardLargeCategory;
    import com.ssafy.infos.BoardMiddleCategory;

    import io.swagger.annotations.Api;
    import io.swagger.annotations.ApiOperation;
    import io.swagger.annotations.ApiParam;
    import io.swagger.annotations.ApiResponse;
    import io.swagger.annotations.ApiResponses;

    @Api(value = "커뮤니티 API", tags = { "Community" })
    @RestController
    @RequestMapping("/api/community")
    public class CommunityController {

        @Autowired
        BoardService boardService;

        @Autowired
        UserService userService;
        
        @PostMapping("/create")
        @ApiOperation(value = "board 글등록", notes = "<strong>글 정보</strong>를 통해 게시글을 추가한다.")
        @ApiResponses({ @ApiResponse(code = 200, message = "성공"), @ApiResponse(code = 401, message = "인증 실패"),
                @ApiResponse(code = 404, message = "게시물 없음"), @ApiResponse(code = 500, message = "서버 오류") })
        public ResponseEntity<? extends BaseResponseBody> createBoard(
                @RequestBody @ApiParam(value = "글 정보", required = true) BoardRequest boardInfo) {
            
            boardService.createBoard(boardInfo);
            return ResponseEntity.status(200).body(BaseResponseBody.of(200, "Success"));
        }
        
        @PostMapping("/reply/create")
        @ApiOperation(value = "board 댓글 등록", notes = "<strong>글 정보</strong>를 통해 댓글을 등록한다.")
        @ApiResponses({ @ApiResponse(code = 200, message = "성공"), @ApiResponse(code = 401, message = "인증 실패"),
                @ApiResponse(code = 404, message = "게시물 없음"), @ApiResponse(code = 500, message = "서버 오류") })
        public ResponseEntity<? extends BaseResponseBody> createReply(
                @RequestBody @ApiParam(value = "글 정보", required = true) BoardRequest boardInfo) {
            if( boardInfo.getCategoryMiddle()==BoardMiddleCategory.QnA.ordinal()) {
                User user = userService.getUserByUid(boardInfo.getUserUid());
                if (user.getAuthority().equals(Authority.MANAGER.toString())) {
                    boardInfo.setCategoryLarge(BoardLargeCategory.COMMUNITY.ordinal());
                    boardInfo.setCategoryMiddle(BoardMiddleCategory.REPLY.ordinal());
                    boardService.createBoard(boardInfo);
                    return ResponseEntity.status(200).body(BaseResponseBody.of(200, "Success"));
                }
                else return ResponseEntity.status(403).body(BaseResponseBody.of(403, "forbidden"));
            }
            else return ResponseEntity.status(400).body(BaseResponseBody.of(403, "bad request"));
        }
        
        
        @GetMapping("/test")
        @ApiOperation(value = "board 검색 정보", notes = "<strong>board 를 title로 검색한 정보</strong>")
        @ApiResponses({ @ApiResponse(code = 200, message = "성공"), @ApiResponse(code = 401, message = "인증 실패"),
                @ApiResponse(code = 404, message = "게시물 없음"), @ApiResponse(code = 500, message = "서버 오류") })
        public ResponseEntity<? extends BoardListResponse> test(
                @ApiParam(value = "board uid", required = true) @RequestParam("title") String title) {
            
            List<Board> boards = boardService.findBoardByTitle(title);
            if (boards==null) {
                return ResponseEntity.status(400).body(BoardListResponse.of(400, "Bad responce",boards));
            }
            else return ResponseEntity.status(200).body(BoardListResponse.of(200, "Success",boards));			
        }
        
        @GetMapping("")
        @ApiOperation(value = "board 검색 정보", notes = "<strong>board 를 uid로 검색한 정보</strong>")
        @ApiResponses({ @ApiResponse(code = 200, message = "성공"), @ApiResponse(code = 401, message = "인증 실패"),
                @ApiResponse(code = 404, message = "게시물 없음"), @ApiResponse(code = 500, message = "서버 오류") })
        public ResponseEntity<? extends BoardResponse> findBoardByUid(
                @ApiParam(value = "board uid", required = true) @RequestParam("uid") int uid) {

            Board board = boardService.findBoardByUid(uid);
            if (board==null) {
                return ResponseEntity.status(400).body(BoardResponse.of(400, "Bad responce",board));
            }
            else return ResponseEntity.status(200).body(BoardResponse.of(200, "Success",board));			
        }
        
        

        @PutMapping("")
        @ApiOperation(value = "board 글 변경 내용", notes = "<strong>board uid로 검색한 board 객체 내용 변경</strong>.")
        @ApiResponses({ @ApiResponse(code = 200, message = "성공"), @ApiResponse(code = 401, message = "인증 실패"),
                @ApiResponse(code = 404, message = "게시물 없음"), @ApiResponse(code = 500, message = "서버 오류") })
        public ResponseEntity<? extends BoardResponse> updateBoardByUid(
                @RequestBody @ApiParam(value = "글 정보", required = true) BoardRequest boardInfo) {
            Board board = boardService.findBoardByUid(boardInfo.getUid());
            
            if (board==null) {
                System.out.println("업데이트 할 게시물이 없습니다.");
                return ResponseEntity.status(400).body(BoardResponse.of(400, "not to do update",null));
            }
            else if(boardInfo.getUserUid()!=board.getUserUid()) 
                return ResponseEntity.status(403).body(BoardResponse.of(403, "your authority can`t change board",board));
            else {
                boardService.updateBoard(board, boardInfo);
                return ResponseEntity.status(200).body(BoardResponse.of(200, "Success",board));
            }
        }
        

        @DeleteMapping("")
        @ApiOperation(value = "board 글 삭제", notes = "<strong>board 를 uid로 검색한 게시글 삭제</strong>.")
        @ApiResponses({ @ApiResponse(code = 200, message = "성공"), @ApiResponse(code = 401, message = "인증 실패"),
                @ApiResponse(code = 404, message = "게시물 없음"), @ApiResponse(code = 500, message = "서버 오류") })
        public ResponseEntity<? extends BaseResponseBody> deleteBoardByUid(
                @ApiParam(value = "board uid", required = true) @RequestParam("uid") int uid) {
            Board board = boardService.findBoardByUid(uid);
            
            if (board==null) {
                System.out.println("삭제 할 게시물이 없습니다.");
                return ResponseEntity.status(400).body(BaseResponseBody.of(400, "not to do update"));
            }
            else if(board.getUserUid()!=board.getUserUid()) 
                return ResponseEntity.status(403).body(BaseResponseBody.of(403, "your authority can`t change board"));
            else {
                boardService.deleteBoardByUid(board);
                return ResponseEntity.status(200).body(BaseResponseBody.of(200, "Success"));
            }
        }
        

        @GetMapping("/list")
        @ApiOperation(value = "board list 정보", notes = "<strong>전체 board 목록</strong>")
        @ApiResponses({ @ApiResponse(code = 200, message = "성공"), @ApiResponse(code = 401, message = "인증 실패"),
                @ApiResponse(code = 404, message = "게시물 없음"), @ApiResponse(code = 500, message = "서버 오류") })
        public ResponseEntity<List<Board>> getBoardList() {
            List<Board> boards = boardService.getAllBoard();
            if (boards==null) 
                return  new ResponseEntity<List<Board>>(boards, HttpStatus.BAD_REQUEST);
            else return new ResponseEntity<List<Board>>(boards, HttpStatus.OK);
        }
    }
    ```
    </details>
- ### service folder
    - ### Service.java
        - Service.java는 DB에 연결하는 ServiceImple의 interface로 구현을 강제하기 위해 작성한다.
        <details>
        <summary span style="font-size:15px">service 예시</summary>
        
        BoardService.java
        ```java
        package com.ssafy.api.service;

        import java.util.List;

        import com.ssafy.api.request.BoardRequest;
        import com.ssafy.db.entity.Board;

        /**
        *	게시판 관련 비즈니스 로직 처리를 위한 서비스 인터페이스 정의.
        */
        public interface BoardService {
            Board createBoard(BoardRequest boardRegisterInfo);
            Board findBoardByUid(int uid);
            Board updateBoard(Board board, BoardRequest boardRegisterInfo);
            void deleteBoardByUid(Board board);
            Board postBoardByUsersNickname(int uid);
            List<Board> getAllBoard();
            List<Board> findBoardByTitle(String title);
        }
        ```
        </details>

    - ### ServiceImpl.java
        - Service.java의 구현체로 직접적인 서비스 로직을 구현한다.
        - Repository의 JPA 함수 또는 QueryDLS의 RepositoryService 클래스를 @Autowired 로 의존성 주입해서 사용한다. 
        <details>
        <summary span style="font-size:15px">ServiceImpl 예시</summary>
        
        BoardServiceImpl.java
        ```java
        package com.ssafy.api.service;

        import java.util.List;
        import java.sql.Timestamp;

        import org.springframework.beans.factory.annotation.Autowired;
        import org.springframework.stereotype.Service;

        import com.ssafy.api.request.BoardRequest;
        import com.ssafy.db.entity.Board;
        import com.ssafy.db.repository.BoardRepository;
        import com.ssafy.db.repository.BoardRepositorySupport;

        @Service("boardService")
        public class BoardServiceImpl implements BoardService {
            @Autowired
            BoardRepository boardRepository;
            
            @Autowired
            BoardRepositorySupport boardRepositorySupport;

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

            @Override
            public Board findBoardByUid(int uid) {
                Board board = new Board();
                board = boardRepositorySupport.findBoardByUid(uid);
                return board;
            }

            
            @Override
            public Board updateBoard(Board board, BoardRequest boardRegisterInfo) {
                
                board.setUid(boardRegisterInfo.getUid());

                board.setTitle(boardRegisterInfo.getTitle());
                board.setContent(boardRegisterInfo.getContent());
                if (boardRegisterInfo.getImg()!=null) {
                    board.setImg(boardRegisterInfo.getImg());			
                }
                board.setRegTime(boardRegisterInfo.getRegTime());
                return boardRepository.save(board);
            }

            
            @Override
            public void deleteBoardByUid(Board board) {
                boardRepository.delete(board);
            }

            @Override
            public Board postBoardByUsersNickname(int uid) {
                return null;
            }
            
            @Override
            public List<Board> getAllBoard() {
                return boardRepository.findAll();
            }
            
            @Override
            public List<Board> findBoardByTitle(String title) {
                return boardRepositorySupport.findBoardByTitle(title);
            }
        }
        ```
        </details>
- ### repository folder
    - ### Repository (사용 권장)
        - JPA 의 Query method, interface를 작성하면 query가 자동으로 생성된다. 
        - JpaRepository<Object,Type> 를 extends한다.
            - [JpaRepository 공식 문서](https://docs.spring.io/spring-data/jpa/docs/current/api/org/springframework/data/jpa/repository/JpaRepository.html)
        - JPA interface 의 함수 규칙에 맞게 함수를 작성한다.
            <details>
            <summary span style="font-size:15px">Repository 예시</summary>

            BoardRepository.java
            ```java
            package com.ssafy.db.repository;

            import org.springframework.data.jpa.repository.JpaRepository;
            import org.springframework.stereotype.Repository;

            import com.ssafy.db.entity.Board;

            @Repository
            public interface BoardRepository extends JpaRepository<Board, Long> {
                Board findBoardByUid(int uid);
                Board findBoardByTitleLike(String title);
            }
            ```

    - ### RepositorySupport
        - JPA의 Query method 로 해결할 수 없는 경우 jpaQueryFactory 를 사용하여 로직을 작성한다.
            <details>
            <summary span style="font-size:15px">RepositorySupport 예시</summary>

            BoardRepositorySupport.java
            ```java
            import org.springframework.beans.factory.annotation.Autowired;
            import org.springframework.stereotype.Repository;

            import com.querydsl.jpa.impl.JPAQueryFactory;
            import com.ssafy.db.entity.Board;
            import com.ssafy.db.qentity.QBoard;

            public class BoardRepositorySupport {
                @Autowired
                private JPAQueryFactory jpaQueryFactory;
                QBoard qBoard = QBoard.board;
                
                public Board findBoardByUid(int uid) {
                    Board board = jpaQueryFactory.select(qBoard).from(qBoard)
                            .where(qBoard.uid.eq(uid)).fetchOne();
                    return board;
                }
            }

            ```
            </details>

- ### qentity folder
    - file naming 규칙: Q + Table name
    - queryfactory를 사용하기 위한 qentity를 정의하는 폴더
    - entity는 entity name 맨 앞에 q를 붙인다.

    <details>
    <summary span style="font-size:15px">QEntity 예시</summary>
    
    QBoard.java
    ```java
    package com.ssafy.db.qentity;

    import static com.querydsl.core.types.PathMetadataFactory.forVariable;

    import java.util.Date;

    import javax.annotation.Generated;

    import com.querydsl.core.types.Path;
    import com.querydsl.core.types.PathMetadata;
    import com.querydsl.core.types.dsl.DatePath;
    import com.querydsl.core.types.dsl.EntityPathBase;
    import com.querydsl.core.types.dsl.NumberPath;
    import com.querydsl.core.types.dsl.StringPath;
    import com.ssafy.db.entity.Board;


    @Generated("com.querydsl.codegen.EntitySerializer")
    public class QBoard extends EntityPathBase<Board> {

        private static final long serialVersionUID = 846542477L;

        public static final QBoard board = new QBoard("board");
        
        public final NumberPath<Integer> uid = createNumber("uid", Integer.class);
        public final NumberPath<Integer> userUid = createNumber("userUid", Integer.class);


        public final NumberPath<Integer> categoryLarge = createNumber("categoryLarge", Integer.class);
        public final NumberPath<Integer> categoryMiddle = createNumber("categoryMiddle", Integer.class);

        public final StringPath title = createString("title");

        public final StringPath content = createString("content");

        // 2022.07.27 13:23 되는 지는 모르지만 일단 syntex 에러는 없음
        public final DatePath<Date> regTime = createDate("regTime", Date.class);

        public final NumberPath<Integer> viewCount = createNumber("viewCount", Integer.class);

        public final StringPath img = createString("img");

        ///////////////
        public QBoard(String variable) {
            super(Board.class, forVariable(variable));
        }

        public QBoard(Path<? extends Board> path) {
            super(path.getType(), path.getMetadata());
        }

        public QBoard(PathMetadata metadata) {
            super(Board.class, metadata);
        }
    }
    ```

- ### response folder
    - Controller에서 front에 정보를 보내는 response 객체입니다.
    - class에 보낼 변수를 선언하고 of 함수를 통해 front 로 보내줄 Object를 리턴합니다.
    - Swagger를 작성합니다.
    
    <details>
    <summary span style="font-size:15px">response 예시</summary>

    BoardResponse.java
    ```java
    package com.ssafy.api.response;

    import java.util.Date;

    import com.ssafy.db.entity.Board;

    import io.swagger.annotations.ApiModel;
    import io.swagger.annotations.ApiModelProperty;
    import lombok.Getter;
    import lombok.Setter;

    @Getter
    @Setter
    @ApiModel("BoardResponse")
    public class BoardResponse{
        @ApiModelProperty(name="board UID", example="1")
        int uid;
        @ApiModelProperty(name="users테이블의 uid", example="2")
        int userUid;
        @ApiModelProperty(name="board 카테고리 대분류 번호", example="2")
        int categoryLarge;
        @ApiModelProperty(name="board 카테고리 중분류 번호", example="1")
        int categoryMiddle;
        @ApiModelProperty(name="board 내용", example="이 편지는 영국에서부터 시작된...")
        String content;
        @ApiModelProperty(name="첨부 이미지 url", example="sfamcslax201381")
        String img;
        @ApiModelProperty(name="조회수", example="2")
        int viewCount;
        @ApiModelProperty(name="등록시각", example="2022-07-29 06:38:57")
        Date regTime;


        public static BoardResponse of(Integer statusCode, String message, Board board) {
            if (board==null) {
                return null;
            }
            else {
                BoardResponse res = new BoardResponse();
                res.setUid(board.getUid());
                res.setUserUid(board.getUserUid());
                res.setCategoryLarge(board.getCategoryLarge());
                res.setCategoryMiddle(board.getCategoryMiddle());
                res.setContent(board.getContent());
                res.setImg(board.getImg());
                res.setRegTime(board.getRegTime());
                res.setViewCount(board.getViewCount());
                board.getUserUid();
                
                return res;
            }
        }
    }
    ```
    </details>

- ### request folder
    - controller 에서 front에서 보내는 변수를 받는 object를 사용합니다.
    - 보안이 필요한 정보들을 front에서 받을 때 작성합니다.
    - 간단한 파라미터를 보낼 경우에는 response 필요 없습니다.
    
    <details>
    <summary span style="font-size:15px">request 예시</summary>

    BoardRequest.java
    ```java
    package com.ssafy.api.request;

    import java.util.Date;

    import io.swagger.annotations.ApiModel;
    import io.swagger.annotations.ApiModelProperty;
    import lombok.Getter;
    import lombok.Setter;
    import lombok.ToString;

    @Getter
    @Setter
    @ToString
    @ApiModel("UserLoginIdRequest")
    public class BoardRequest {
        
        @ApiModelProperty(name = "글번호 UID", example = "1")
        int uid;

        @ApiModelProperty(name = "user table의  UID", example = "1")
        int userUid;

        @ApiModelProperty(name = "카테고리 분류(1:공지, 2:소식, 3: 커뮤니티 ,4: 게임 가이드, 5:댓글)", example = "1")
        int categoryLarge;

        @ApiModelProperty(name = "카테고리 중분류(1.공지, 2.점검, 3.패치 등...)", example = "1")
        int categoryMiddle;

        @ApiModelProperty(name = "글 제목", example = "명지 방 양도받으실분 구함")
        String title;

        @ApiModelProperty(name = "글 내용", example = "명지방 300/30 오션뷰 ㅍㅍ")
        String content;

        @ApiModelProperty(name = "글 등록 시각", example = "2022-07-26 23:35:43")
        Date regTime;

        @ApiModelProperty(name = "조회수", example = "10")
        int viewCount;
        
        @ApiModelProperty(name = "첨부 이미지(url형식)", example = "mxdfaogckvjaof")
        String img;
    }
    ```
    </details>

- ### config 폴더
    - 프로그램에서 공통적으로 사용할 값을 Config 파일에 작성합니다.
    - (config 부분은 따로 정리 필요)

- ### build.gradle 파일
    - 스프링 프레임워크 환경 설정과 배포를 위한 정보들이 있습니다.


    <details>
    <summary span style="font-size:15px">build.gradle 예시</summary>

    build.gradle
    ```gradle
    import org.apache.tools.ant.filters.ReplaceTokens

    buildscript{
        ext {
            springBootVer = '2.4.5'
            querydslVer = '4.4.0'
            querydslPluginVer = '1.0.10'
            springDependencyMgmtVer = '1.0.11'
            springLoadedVer = '1.2.8'
            nodePluginVer = '1.3.1'
        }
        repositories {
            mavenCentral()
            jcenter()
        }
        dependencies {
            classpath "org.springframework.boot:spring-boot-gradle-plugin:${springBootVer}"
            classpath "io.spring.gradle:dependency-management-plugin:${springDependencyMgmtVer}.RELEASE"
            classpath "org.springframework:springloaded:${springLoadedVer}.RELEASE"
            //이클립스인 경우를 위한 QueryDSL 플러그인. IntelliJ는 불필요. [시작]
            classpath "gradle.plugin.com.ewerk.gradle.plugins:querydsl-plugin:${querydslPluginVer}"
            //[끝] 
            classpath "com.github.node-gradle:gradle-node-plugin:3.1.0"
        }
    }

    plugins {
        id 'java'
        id 'idea'
        id 'org.springframework.boot' version "${springBootVer}"
        id "base"
    }

    apply plugin: 'io.spring.dependency-management'
    apply plugin: 'eclipse'
    apply plugin: 'com.github.node-gradle.node'
    //이클립스인 경우를 위한 QueryDSL 플러그인. IntelliJ는 불필요.
    apply plugin: 'com.ewerk.gradle.plugins.querydsl'


    repositories {
        mavenCentral()
        maven { url 'https://repo.spring.io/snapshot' }
        maven { url 'https://repo.spring.io/milestone' }
        maven { url "https://repo.spring.io/libs-release" }
        maven { url "https://repo.maven.apache.org/maven2" }
        maven { url "https://build.shibboleth.net/nexus/content/repositories/releases" }
    }

    group 'com.ssafy'
    version '1.0-SNAPSHOT'
    sourceCompatibility = '1.8'

    node {
        download = true
        version = '14.17.0'
        // Set the work directory where node_modules should be located
        nodeModulesDir = file("${project.projectDir}/../frontend")
    }

    configurations {
        providedRuntime
    }

    /*task npmInstall(type: NpmTask, overwrite: true) {
        args = ['install']
    }*/

    // task webpack(type: NpmTask, dependsOn: 'npmInstall') {
    //     args = ['run','build']
    // }

    // processResources is a Java task. Run the webpack bundling before this task using the 'build' task in the package.json
    // processResources.dependsOn 'webpack'

    //set build time and inject value to application.properties
    def buildTime() {
        def date = new Date()
        def formattedDate = date.format('yyyyMMdd_HHmm')
        return formattedDate
    }

    project.ext.set("build.date", buildTime())

    processResources {
        with copySpec {
            from "src/main/resources"
            include "**/application*.yml"
            include "**/application*.yaml"
            include "**/application*.properties"
            project.properties.findAll().each {
                prop ->
                    if (prop.value != null) {
                        filter(ReplaceTokens, tokens: [ (prop.key): String.valueOf(prop.value)])
                        filter(ReplaceTokens, tokens: [ ('project.' + prop.key): String.valueOf(prop.value)])
                        filter(ReplaceTokens, tokens: [ ('project.ext.' + prop.key): String.valueOf(prop.value)])
                    }
            }
        }
    }

    //이클립스인 경우를 위한 QueryDSL 설정. IntelliJ는 불필요. [시작]
    def querydslDir = 'src/main/generated'
    querydsl {
        library = "com.querydsl:querydsl-apt"
        jpa = true
        querydslSourcesDir = querydslDir
    }

    sourceSets {
        main {
            java {
                srcDirs = ['src/main/java', querydslDir]
            }
        }
    }

    compileQuerydsl {
        options.annotationProcessorPath = configurations.querydsl
    }

    configurations {
        querydsl.extendsFrom compileClasspath
    }
    //[끝]

    dependencies {
        implementation("org.springframework.boot:spring-boot-starter-web")
        implementation("org.springframework.boot:spring-boot-starter-websocket")
        implementation("org.springframework.boot:spring-boot-starter-security")
        implementation("org.springframework.boot:spring-boot-starter-data-jpa")
        implementation("org.springframework.boot:spring-boot-starter-actuator")
        implementation("org.springframework.plugin:spring-plugin-core:2.0.0.RELEASE")
        testImplementation("org.springframework.security:spring-security-test")
        annotationProcessor("org.springframework.boot:spring-boot-starter-data-jpa")
        runtimeOnly("mysql:mysql-connector-java")
        developmentOnly("org.springframework.boot:spring-boot-devtools")
        annotationProcessor("org.springframework.boot:spring-boot-configuration-processor")

        implementation('commons-io:commons-io:2.6')
        implementation("org.apache.commons:commons-collections4:4.4")
        implementation("org.apache.commons:commons-lang3:3.9")

        implementation("com.querydsl:querydsl-jpa:${querydslVer}")
        implementation("com.querydsl:querydsl-apt:${querydslVer}")

        //STOMP 웹소캣 서버 사이드 테스트를 위한 의존성 추가
        implementation("org.springframework.boot:spring-boot-starter-mustache")
        //STOMP 관련 프론트 라이브러리
        implementation('org.webjars.bower:jquery:3.3.1')
        implementation('org.webjars:sockjs-client:1.1.2')
        implementation('org.webjars:stomp-websocket:2.3.3-1')
        implementation('org.webjars:webjars-locator:0.30')
        //WebRTC 클라이언트 의존성 추가
        implementation('org.webjars.bower:webrtc-adapter:7.4.0')
        //Kurento (미디어서버) 관련 의존성 추가
        implementation('org.kurento:kurento-client:6.16.0')
        implementation('org.kurento:kurento-utils-js:6.15.0')


        //IntelliJ용
        //IntelliJ에서는 하기 annotationProcessor를 쓰면 별도의 querydsl 플러그인 및 플러그인 설정이 불필요.
        //annotationProcessor("com.querydsl:querydsl-apt:${querydslVer}:jpa")

        implementation("com.squareup.retrofit2:retrofit:2.7.1")
        implementation("com.squareup.retrofit2:converter-jackson:2.7.1")
        implementation("com.squareup.okhttp3:logging-interceptor:3.9.0")

        implementation("com.google.guava:guava:29.0-jre")
        annotationProcessor("com.google.guava:guava:29.0-jre")

        testImplementation("com.jayway.jsonpath:json-path:2.4.0")

        implementation("com.auth0:java-jwt:3.10.3")
        
        implementation("io.springfox:springfox-swagger2:3.0.0")
        implementation("io.springfox:springfox-swagger-ui:3.0.0")
        implementation("io.springfox:springfox-data-rest:3.0.0")
        implementation("io.springfox:springfox-bean-validators:3.0.0")
        implementation("io.springfox:springfox-boot-starter:3.0.0")

        compile("javax.annotation:javax.annotation-api:1.2")

        implementation("org.projectlombok:lombok:1.18.20")
        annotationProcessor("org.projectlombok:lombok:1.18.20")

        testCompile('org.springframework.boot:spring-boot-starter-test')
    }

    test {
        useJUnitPlatform()
    }

    bootJar{
        mainClassName = 'com.ssafy.GroupCallApplication'
    }

    bootRun {
        String activeProfile = System.properties['spring.profiles.active']
        systemProperty "spring.profiles.active", activeProfile
    }
    ```
    </details>