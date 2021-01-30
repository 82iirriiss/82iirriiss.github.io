---
sort: 1
---

# 설치

## 1. Intellij 버전과 다운로드

- 다운로드: <https://www.jetbrains.com/idea/>
- community 버전 무료 : 프레임워크, 데이터베이스등의 툴이 없음

## 2. openjdk 설치
- 다운로드 및 설치 : <https://adoptopenjdk.net/>
- jdk 버전 확인
- 삭제가 필요하면? : /Library/java/JavaVirtualMachines 의 *.jdk를 삭제한다 `sudo rm -rf *.jdk`

```
➜  ~ java -version
java version "1.8.0_281"
Java(TM) SE Runtime Environment (build 1.8.0_281-b09)
Java HotSpot(TM) 64-Bit Server VM (build 25.281-b09, mixed mode)
```

## 3. Gradle 설치

- Maven과 같은 빌드도구
- 다운로드 : <https://gradle.org/install/> - 'Binary-only' 다운로드
- 로컬에 압축 풀기
- .zshrc 에 path 추가하기

```bash
$ vim .zshrc
 GRADLE_HOME=/Users/kim-yunmi/gradle-6.8.1
 export PATH=$HOME/bin:{GRADLE_HOME}/bin:$PATH
# esc, :wq 로 저장
# 쉘 적용
$ source .zshrc
```

- 버전체크

```bash
$ gradle --v
```

```
------------------------------------------------------------
Gradle 6.8.1
------------------------------------------------------------

Build time:   2021-01-22 13:20:08 UTC
Revision:     31f14a87d93945024ab7a78de84102a3400fa5b2

Kotlin:       1.4.20
Groovy:       2.5.12
Ant:          Apache Ant(TM) version 1.10.9 compiled on September 27 2020
JVM:          11.0.10 (AdoptOpenJDK 11.0.10+9)
OS:           Mac OS X 10.16 x86_64
```