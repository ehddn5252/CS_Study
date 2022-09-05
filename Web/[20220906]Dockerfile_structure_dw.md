# Docker file structure

목적: Dockerfile 구조를 이해하고 직접 도커파일을 작성할 수 있다.

Dockerfile은 인프라 구성을 효을적으로 관리하기 위한 파일로, 쉘 파일과 비슷한 dockerfile을 작성해서 해당 도커 이미지를 사용자의 환경에 맞춰서 운영할 수 있도록 합니다.

## 도커 명령어
- 보통 도커를 사용할 때에 커스텀 이미지는 Dockerfile을 사용해서 작성합니다. Dockerfile은 도커 이미지 생성을 위한 DSL입니다. Dockerfile에서 사용할 수 있는 20개 남짓한 키워드들이 있습니다. 
- 여기에선 자주 사용하는 약 10개의 명령어에 대해서 정리합니다

```Dockerfile
INSTRUCTION <arguments>
```
- 도커파일은 모두 위와 같은 구조로 되어 있습니다.
- 명령은 대소문자를 구분하지 않습니다. 그러나 관례는 인수와 더 쉽게 구별하기 위해 대문자를 사용하는 것입니다.

명령어를 하나 하나 알아봅니다.

## 1. FROM 
```Dockerfile
# FROM <IMAGE_NAME>
FROM ubuntu16:04
```
- IMAGE_NAME에는 커스텀 이미지의 기반이 되는 이미지 이름을 지정해줍니다.
- FROM 은 항상 Dockerfile의 맨 첫줄에 옵니다.
- 뒤에 tag가 없다면 latest 버전이 적용됩니다.

## 2. RUN
```Dockerfile
# RUN <COMMAND>
RUN apt update
```
- RUN 은 명령어를 실행하는 키워드입니다.
- cmd 창에서 사용하는 명령어로 생각하시면 됩니다.
    ### double layer or one layer

    ```Dockerfile
    # 아래의 도커파일은 RUN 명령어를 2번쓰지 않고 && 명령어로 한번에 쓰는 것을 권장(layer를 줄일 수 있다)
    FROM ubuntu:18.04
    RUN apt update
    RUN apt install -y git
    ```

    ```Dockerfile
    # 권장 방법
    FROM ubuntu:18.04
    RUN apt update && apt install -y git
    ```


## 3. ADD (COPY와 비슷함)
```Dockerfile
#ADD [--chown=<user>:<group>] <src>... <dest>
#ADD [--chown=<user>:<group>] ["<src>",... "<dest>"]

## ADD <호스트OS 파일 경로> <Docker 컨테이너 안에서의 경로>
ADD test.sh /root/add/test.sh
```

-  호스트OS의 파일 또는 디렉토리를 컨테이너 안의 경로로 복사 합니다.

- <src>가 대상 컨테이너 내부에 복사될 <dest>절대 경로 또는 에 대한 상대 경로 입니다
- ADD의 경우 원격 파일 다운로드 또는 압축 해제 등과 같은 기능을 갖고 있습니다

### 예시
"hom"으로 시작하는 모든 파일을 추가하려면:
```Dockerfile
ADD hom* /mydir/
```
- 아래 ?예에서 는 "home.txt"와 같은 단일 문자로 대체됩니다.

```Dockerfile
ADD hom?.txt /mydir/
```
소스가 대상 컨테이너 내부에 복사될 <dest>절대 경로 또는 에 대한 상대 경로 입니다 .WORKDIR

아래 예는 상대 경로를 사용하고 "test.txt"를 <WORKDIR>/relativeDir/다음 에 추가합니다.
```Dockerfile
ADD test.txt relativeDir/
```
## 4. Copy (ADD와 비슷함)

```Dockerfile

# COPY [--chown=<user>:<group>] <src>... <dest>
# COPY [--chown=<user>:<group>] ["<src>",... "<dest>"]

## COPY <호스트OS 파일 경로> <Docker 컨테이너 안에서의 경로>
COPY test.sh /root/copy/test.sh
```
- 호스트OS의 파일 또는 디렉토리를 컨테이너 안의 경로로 복사 합니다.
- COPY의 경우 호스트OS에서 컨테이너 안으로 복사만 가능하지만, 
- ADD의 경우 원격 파일 다운로드 또는 압축 해제 등과 같은 기능을 갖고 있습니다. 즉, 호스트OS에서 컨테이너로 단순히 복사만을 처리할 때 COPY를 사용 합니다.

## 5. ENV, ARG
- ENV와 ARG는 비슷해 보이지만 다른 명령 입니다.
- ENV는 Dockerfile 또는 컨테이너 안에서 환경 변수로 사용이 가능하고, ARG는 Dockerfile 에서만 사용이 가능 합니다.
```Dockerfile
# ENV 사용 법

# ENV [key] [value]
ENV name dw
ENV favoritFood pizza

# ENV [key]=[value]  ## 한번에 여러개의 값을 설정할 때 사용
ENV name=dw \
    favoritFood=pizza
```

![image](https://user-images.githubusercontent.com/51036842/188453606-e34d332a-ef23-4968-b7da-99fd259a1366.png)

때문에 ARG의 경우 Dockerfile 작성하는데에 필요한 변수를 선언하여 Dockerfile을 좀 더 편하게 작성할 수 있고, ENV는 변수 선언은 물론, 컨테이너 안에서 사용할 환경 변수(작업 디렉토리 지정) 등 선언하여 사용할 수 있습니다. 
```Dockerfile
# base 가 되는 이미지입니다.
FROM ubuntu:16.04
# 현재 폴더의 test.sh를 컨테이너의 /root/mkdir의 test.sh 파일로 복사합니다
COPY test.sh /root/mkdir/test.sh
# 변수 설정합니다.
ENV DIR=/root/mkdir/
# 위의 /root/mkdir/ 를 echo로 불러옵니다.
RUN echo ${DIR}
# 컨테이너가 시작될 때마다 실행할 명령어 입니다.
CMD ${DIR}/test.sh
```

## 6. CMD, ENTRYPOINT
- 두 명령어 모두 이미지를 바탕으로 생성된 컨테이너에서 사용됩니다.(docker run or docker start)

    ## CMD
    - CMD 명령어는 컨테이너가 시작될 때마다 시랭할 명령어입니다.
    - centos 7 버전을 베이스로하고 nginx를 설치를 하면 nginx를 실행시켜 줘야합니다. 이때 쓰는 것이 CMD 또는 ENTRYPOINT 입니다.

    다음 코드는 ubuntu:18.04 을 깔고, nginx 를 설치 한 후, systemctl start nginx 를 입력해주는 것과 같습니다.

    ```Dockerfile
    FROM ubuntu:18.04

    RUN touch /etc/yum.repos.d/nginx.repo && echo -e '[nginx]\nname=nage repo\nbaseurl=http://nginx.org/packages/centos/7/$basearch/\ngpgcheck=0\nenabled=1' > /etc/yum.repos.d/nginx.repo

    RUN yum -y install nginx

    # nginx foreground 실행
    CMD ["nginx", "-g", "daemon off;"]
    ```

    - CMD와 ENTRYPOINT의 차이는 docker run 을 사용하며 새로운 명령을 지정한 경우 이 명령이 실행 되는지, 안되는지의 차이 입니다.

    - CMD는 컨테이너가 실행될 때 명령어 및 인자값을 전달하여 실행하고 DOCKER RUN 명령에 쉘 명령어 및 인자값을 전달할 경우에는 CMD에 작성된 명령어와 인자값은 무시됩니다.
    - 우선순위는 docker run > CMD
    ## ENTRYPOINT

    - ENTRYPOINT는 컨테이너가 실행될 때 명령어 및 인자값을 전달하여 실행, 단 docker run 명령어 및 인자값 전달할 경우에는 쉘명령어는 영향을 받지 않으며, 인자값만 영향을 받습니다.

    - 우선순위는 ENTRYPOINT(인자값) < DOCKER RUN < ENTRYPOINT(명령어)

    - ENTRYPOINT는 conatiner run의 --entrypoint="실행명령" 옵션을 사용하여 Dockerfile에 정의한 ENTRYPOINT를 무시할 수 있습니다.
    
    ```Dockerfile
    FROM ubuntu:18.04

    ENTRYPOINT ["/bin/ls", "-lh", "/root"]
    ```

    위 스크립트는 --entrypoint 옵션을 사용하여 ent_hw 이미지를 생성 및 실행하며 cat /etc/hosts 를 수행 합니다.

    ### 정리
    
    - CMD는 명령과 인자값이 변경될 수 있고, 컨테이너에서 명령 설정하지 않을 시 CMD에 기재된 명령을 기본값으로 실행됩니다. 
    - ENTRYPOINT는 명령 수정이 불가능하여 사용자에 의해 변경되지 않고 고정적으로 실행될 명령은 ENTRYPOINT를 사용하는것이 좋습니다.

## 7. Workdir
- 명령을 실행하기 위한 디렉토리를 지정합니다.
- window에서 cd로 명령 실행하는 곳을 이동하는 것이라고 생각하면 됩니다.
```dockerfile
FROM ubuntu:16.04

COPY test.sh /root/mkdir/test.sh
WORKDIR /root/mkdir/

CMD ./test.sh
```

## 8. EXPOSE
- EXPOSE 명령어는 해당 컨테이너가 런타임에 지정된 네트워크 포트에서 수신 대기중이란 것을 알려줍니다. 
- 일반적으로 dockerfile을 작성하는 사람과 컨테이너를 직접 실행할 사람 사이에서 공개할 포트를 알려주기 때문에 문서 유형으로 작성합니다.
- 실제로 listening 상태로 올려주지는 않아서 실제로 포트를 열기 위해서는 container run 에서 -p 옵션을 사용해야 합니다.

```
# EXPOSE 포트번호[/protocol]

EXPOSE 80/tcp
EXPOSE 80/udp
```
위와 같이 선언이 되지만 프로토콜을 지정하지 않으면 기본값은 TCP 입니다.

## 9. Volumn
- 빌드된 이미지로 컨테이너를 생성했을 때 호스트와 공유할 컨테이너 내부의 디렉터리를 설정합니다.
- 볼륨은 Docker 컨테이너에서 생성하고 사용하는 데이터를 유지하기 위한 기본 메커니즘입니다. 
- VOLUMN ["/home/dir", "home/dir2"] 와 같이 JSON 배열의 형식으로 여러 개 사용하거나 VOLUMNE /home/dir /home/dir2 로도 사용할 수 있습니다. 

![image](https://user-images.githubusercontent.com/51036842/188491266-06d6655a-784a-4928-a8d8-a1e831027379.png)

- 다음 예시는 컨테이너 내부의 /home/volumne 디렉터리를 호스트와 공유하도록 설정합니다.

```Dockerfile
FROM ubuntu:18.04
RUN mkdir /home/volumne
RUN echo test >> /home/volumne/testfile
VOLUME /home/volumne
```

# Docker build 명령어

## Dockerfile build

도커파일을 작성했으면 이 도커 파일로 이미지를 만들 수 있습니다. 이미지를 만드는 명령어는 다음과 같습니다.

Dockerfile 이 있는 곳
```python
# 도커 image 생성
docker build

# 도커 이미지 생성 + 이미지 이름 지정
# docker build -t <이미지 이름>
docker build -t myImage:0.1
```
- 간단히 docker build 명령어만 실행시키면 이미지가 생성됩니다. 여기에서 이미지 이름을 추가하려면 -t 옵션을 더 사용합니다

- 다음에는 도커 명령어에 대해서 정리하겠습니다.

## 참고 자료
- https://nirsa.tistory.com/66?category=868315
- [도커 파일 레퍼런스](https://docs.docker.com/engine/reference/builder/)
- https://devlog-wjdrbs96.tistory.com/296
- https://nirsa.tistory.com/69
- https://www.44bits.io/ko/post/building-docker-image-basic-commit-diff-and-dockerfile
- 시작하세요! 도커/쿠버네티스(용찬호 지음)