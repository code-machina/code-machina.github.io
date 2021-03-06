---
layout: post
title:  "Golang 즐기기"
subtitle: "Visual Studio Code 에서 Golang 환경 설정"
author: "코마"
date:   2019-06-10 00:00:00 +0900
categories: [ "Go", "DI", "DP"]
excerpt_separator: <!--more-->
---

안녕하세요 **코마**입니다. 오랜만에 글을 쓰는데요. 오늘은 Visual Studio Code 에서 Golang 을 설정하는 방법을 알아보겠습니다. 😺

<!--more-->

## 설치 : Golang

Golang 개발을 시작하기 위해서는 Golang 실행 파일을 다운받아야 합니다. Google 에서 "Golang" 을 검색하면 간단히 다운로드가 가능합니다.

<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<!-- 수평형 광고 -->
<ins class="adsbygoogle"
     style="display:block"
     data-ad-client="ca-pub-7572151683104561"
     data-ad-slot="5543667305"
     data-ad-format="auto"
     data-full-width-responsive="true"></ins>
<script>
     (adsbygoogle = window.adsbygoogle || []).push({});
</script>

## 설치 : Visual Studio Code _ Go Extension

저는 개발 툴로 Visual Studio Code 를 사용합니다. Go 관련하여 여러 IDE 가 있지만 무료 중에서 가장 좋은 툴이라고 생각합니다. 
vscode 를 설치하는 방법은 다루지 않겠으나, Go Extension 을 설치하는 방법은 간단히 다루도록 하겠습니다.

1. vscode 에서 확장 설치 메뉴를 클릭
2. Go 를 검색
3. 최 상단에 있는 확장(Extension)을 설치
4. 아래에 기입한 명령어를 CMD 를 통해서 설치
5. GO 확장 설치 관련 종속성 설치
6. vscode 를 실행 중이라면 재시작 합니다. (환경 변수 업데이트)

4번 항목에서 주의할 점은 go.exe 경로가 실행 경로에 포함되어 있는지 여부를 판별하는 것입니다. 이를 위해서 CMD 창을 열고 아래의 명령어를 실행해 봅니다.

별도의 경로에 설치하지 않았다면 아래와 같이 설치되게 됩니다. 이로써 설치가 정상적으로 되었음을 확인하였으므로 종속성 설치를 합니다.

```cmd
> where go
C:\Go\bin\go.exe
```

아래의 명령어를 복사-붙여넣기 합니다.

```powerhsell
go get -u -v github.com/ramya-rao-a/go-outline
go get -u -v github.com/acroca/go-symbols
go get -u -v github.com/mdempsky/gocode
go get -u -v github.com/rogpeppe/godef
go get -u -v golang.org/x/tools/cmd/godoc
go get -u -v github.com/zmb3/gogetdoc
go get -u -v golang.org/x/lint/golint
go get -u -v github.com/fatih/gomodifytags
go get -u -v golang.org/x/tools/cmd/gorename
go get -u -v sourcegraph.com/sqs/goreturns
go get -u -v golang.org/x/tools/cmd/goimports
go get -u -v github.com/cweill/gotests/...
go get -u -v golang.org/x/tools/cmd/guru
go get -u -v github.com/josharian/impl
go get -u -v github.com/haya14busa/goplay/cmd/goplay
go get -u -v github.com/uudashr/gopkgs/cmd/gopkgs
go get -u -v github.com/davidrjenni/reftools/cmd/fillstruct
go get -u -v github.com/alecthomas/gometalinter
gometalinter --install
```

```powershell
go get -u github.com/go-delve/delve/cmd/dlv
```

## Golang : 실행 확인

powershell 화면을 실행하고 아래의 명령어를 실행합니다.

```powershell
mkdir tmp_golang
cd tmp_golang
code .
```

아래의 소스 파일을 만들고 `Ctrl + F5` 를 조합키를 눌러주세요.

```golang
package main

import "fmt"

func main() {
  fmt.Printf("Hello\n")
}
```

vscode 가 동작하면서 `Hello` 메시지를 출력하면 성공입니다.

## 참고

- [Visual Studio Code : Golang 확장 설치](https://github.com/Microsoft/vscode-go/wiki/Go-tools-that-the-Go-extension-depends-on)

## 결론

이번 시간에는 golang 을 개발할 수 있는 IDE 를 설정해 보았습니다. 시작이 반이라고 합니다. 즐거운 코딩 되시길 바랍니다.

지금까지 **코마**의 훈훈한 블로그 였습니다. 구독해 주셔서 감사합니다. 더욱 발전하는 모습을 보여드리기 위해 노력하도록 하겠습니다.
