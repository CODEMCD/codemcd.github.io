---
title: "[jsdsLib]jsDelivr"
excerpt: "오픈소스 CDN jsDelivr"
categories:
  - Toy
tags:
  - Toy_jsdsLib
search: true
sidebar_main: true
toc: true
---

이 문서는 jsDelivr 을 사용하는 방법에 대해 번역한 것입니다.

>> 오타, 잘못된 번역이 많을 수 있습니다. 감안하고 읽어주세요...

원문
- [jsDelivr 공식 Gitgub](https://github.com/jsdelivr/jsdelivr)

공식 사이트
- <https://www.jsdelivr.com/>

## jsDelivr - Open Source CDN
관련 프로젝트:
- [jsDelivr API](https://github.com/jsdelivr/data.jsdelivr.com)
- [jsDelivr website](https://github.com/jsdelivr/www.jsdelivr.com)

jsDelivr 관계자들은 도와줄 사람을 찾고 있습니다. 위의 링크(repos)를 열어 이슈를 확인해주세요. 해당 이슈에 관한 아이디어가 있으면 댓글을 달아주시고, 새로운 아이디어가 있다면 새로운 이슈를 만들어주세요.

**Note: jsDelivr 의 백엔드(Back end)가 변경되었습니다. 그에 따라 새로운 jsDelivr 사용방법을 알아야 합니다.**

jsDelivr 은 무료 오픈소스 파일을 위한 무료 CDN 입니다. jsDelivr 은 Github, npm과 밀접하게 통합되어 있습니다. 그러므로 외부의 대부분 오픈소스 프로젝트에 CDN 서비스를 제공할 수 있고, 이 서비스는 자동화되어 있고 신뢰할만합니다.

jsDelivr 은 엄청난 양의 트래픽을 가지고 있는 인기있는 웹사이트 제작에도 사용할 수 있는 안정적인 CDN 서비스를 제공합니다. jsDelivr 은 대역폭(bandwidth)의 제한이나 프리미엄 기능이 없고, 누구나 자유롭게 사용할 수 있습니다.

## 사용법(Usage)
jsDelivr 은 공공 저장소의 모든 npm 패키지에서 모든 파일을 즉시 서비스 할 수 있습니다.

npm 으로 배포된 새로운 버전의 패키지 역시 즉시 CDN 을 통해 바로 사용가능합니다. 그리고 사용자 개인의 유지보수를 할 필요가 없습니다.

만약 패키지나 버전, 파일이 npm 에서 제거된다고 해도 jsDelivr 에서는 어떤 웹사이트에도 영향없이 계속 서비스를 제공합니다. 이는 해당 파일들이 jsDelivr 내부 저장소에 영구적으로 저장되어 있기 때문입니다.

### npm
배포된 npm 패키지를 불러오기 위한 기본 양식
```
/npm/package@version/file
```

정확한 버전 불러오기
```
/npm/jquery@3.1.0/dist/jquery.min.js
```

버전의 일부분만 사용하여 불러오기
```
/npm/jquery@3/dist/jquery.min.js
/npm/jquery@3.1/dist/jquery.min.js
```

태그로 불러오기(추천하지 않음)
```
/npm/jquery@beta/dist/jquery.min.js
```

버전을 완전히 생략하거나 "latest"(가장 최신 버전) 로 불러오기(개발 환경에서만 사용할 것)
```
/npm/jquery@latest/dist/jquery.min.js
/npm/jquery/dist/jquery.min.js
```

미니 버전이 없는 경우 js/css 파일에 ```.min``` 추가한다면 자동적으로 생성됩니다. 그리고 만들어진 모든 파일들은 소스 맵을 제공하고 개발하는 동안 쉽게 사용할 수 있습니다.
```
/npm/github-markdown-css@2.4.1/github-markdown.min.css
```

### GitHub
Github release 또는 commit 불러오기

```
/gh/user/repo@version/file
```

정확한 버전 불러오기

```
/gh/jquery/jquery@3.1.0/dist/jquery.min.js
/gh/jquery/jquery@32b00373b3f42e5cdcb709df53f3b08b7184a944/dist/jquery.min.js
```

정확한 버전 대신 버전 범위 불러오기(반드시 유효한 semver 버전을 사용해야함.)

```
/gh/jquery/jquery@3/dist/jquery.min.js
/gh/jquery/jquery@3.1/dist/jquery.min.js
```

버전을 완전히 생략하거나 "latest"(가장 최신 버전) 로 불러오기(개발 환경에서만 사용할 것)

```
/gh/jquery/jquery@latest/dist/jquery.min.js
/gh/jquery/jquery/dist/jquery.min.js
```

미니 버전이 없는 경우 js/css 파일에 ```.min``` 추가한다면 자동적으로 생성됩니다. 그리고 만들어진 모든 파일들은 소스 맵을 제공하고 개발하는 동안 쉽게 사용할 수 있습니다.

```
/gh/sindresorhus/github-markdown-css@v2.4.1/github-markdown.min.css
```

디렉토리 리스트 가져오기

```
/gh/jquery/jquery@3.1.0/
/gh/jquery/jquery@3.1.0/dist/
```

### 예제

1. npm

jsdsLib 패키지 1.1 버전 불러오기
```
<head>
    <meta charset="utf-8" />
    <script src="https://cdn.jsdelivr.net/npm/jsdslib@1.1/dist/List.min.js"></script>
</head>
```

2. Github

Github 에서 cdn 서비스를 이용하기 위해서는 먼저, 코드 깃허브에 올리고 버전 태그(lightweight tag)설정한다.
- ```git tag [version]``` -> ```git push origin [version]```

jsdsLib 1.1 버전 불러오기
```
<head>
    <meta charset="utf-8" />
    <script src="https://cdn.jsdelivr.net/gh/CODEMCD/jsdsLib@1.1/dist/List.min.js"></script>
</head>
```
