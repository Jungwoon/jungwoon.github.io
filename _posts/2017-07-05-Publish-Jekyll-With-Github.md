---
layout: post
title: "GitHub를 이용해서 블로그 하기 #3"
image: '/assets/img/'
description: 'Jekyll을 이용해서 블로그 하기'
tags:
- Jekyll
- Blogging
categories:
- Jekyll
---

`GitHub를 이용해서 블로그 하기 #2` 까지 잘 따라했으면 로컬에 Jekyll 설치가 끝났을 겁니다.
이제 아래 명령어를 통해서 새로운 Jekyll 프로젝트를 만듭니다.

```

$ jekyll new [본인 프로젝트 명]

```

![](https://cdn-images-1.medium.com/max/800/1*-Bke4HqcrlYotTzilLl9ew.png)

그럼 아래와 같이 설치가 진행될것입니다.

![](https://cdn-images-1.medium.com/max/1200/1*WlQz9z6BihM0BGjYimIRDg.png)

그 다음 생성된 디렉토리로 가서 확인해보면 기본적으로 필요한 파일들이 생성된것을 확인하실 수 있을 것입니다.

![](https://cdn-images-1.medium.com/max/1200/1*1C8qGJ1OlRGMjtaUTitZ8g.png)

다음 명령을 입력해서 로컬에서 Jekyll이 잘 동작하는지 확인해 봅니다.

```

$ jekyll serve

```

![](https://cdn-images-1.medium.com/max/1200/1*9PO6LE-Ww-oO6ARQEOPzSA.png)

그 다음 브라우저를 열고 `http://localhost:4000` 으로 들어가면 아래와 같이 기본 템플릿을 가진 페이지가 생성된것을 확인하실 수 있습니다.

![](https://cdn-images-1.medium.com/max/1200/1*QwamW1R_79MkTZb2dNNxJg.png)

실제 사용할 건 기본 템플릿이 아니기 때문에, 터미널에서 `Ctrl + C`를 눌러서 서버를 종료 해주고, 해당 디렉토리를 열어서 안에 기본적으로 생성된 파일들을 전부 지워줍니다.

** Mac은 해당 터미널에서 아래 명령어를 입력하면, Finder에서 열립니다.
```

$ open . # '.'는 현재 디렉토리를 의미합니다.
```

![](https://cdn-images-1.medium.com/max/1200/1*JokTyXihafQu3ta-D7EOKA.png)

다음과 같이 Finder에서 열리면 안에 파일을 전부 삭제 해줍니다.

![](https://cdn-images-1.medium.com/max/1200/1*QyywRWUnsFiNawNVyZQNeg.png)

그 다음 Jekyll 테마를 찾기 위해 [http://jekyllthemes.org](http://jekyllthemes.org)로 이동하여 마음에 드는 테마를 찾아서 선택합니다.

![](https://cdn-images-1.medium.com/max/1200/1*EARSMs68EjJDmGbPS506dA.png)

그 다음 Download를 눌러서 파일을 다운 받습니다.

![](https://cdn-images-1.medium.com/max/1200/1*yh6KSUMMfXaPJZ-GgGoR-w.png)

Zip 형태로 다운이 받아지며 압축을 풉니다.

![](https://cdn-images-1.medium.com/max/1200/1*oAl68XWu8NZy00yyCiJk1Q.png)

압춘을 푼 후 다운받은 폴더에 있는 모든 파일을 복사하여, 처음에 만들었던 프로젝트 폴더에 복사를 합니다.

![](https://cdn-images-1.medium.com/max/1200/1*ecfcJylW9L6DJOcDrCFq2Q.png)

![](https://cdn-images-1.medium.com/max/1200/1*dSXtaOftPffAgrzHmkd2nA.png)


잘 적용이 되었는지 확인을 하기 위해 처음과 같이 아래 명령어를 통해서 Local에서 테스트를 합니다.

```
$ jekyll serve
```

![](https://cdn-images-1.medium.com/max/1200/1*FwRk24IKGbVCfLv51Y3fvA.png)

아래와 같이 잘 적용이 되었네요

![](https://cdn-images-1.medium.com/max/1200/1*jRqom41eYBoNv3ZB5nKEDw.png)

그럼 `Ctrl + C`를 눌러서 Local 서버를 종료시켜주고, 본인의 [Github](https://github.com) 으로 가서 블로그를 위한 repository의 URL을 복사합니다.

![](https://cdn-images-1.medium.com/max/1200/1*SSWbPwPN81_hoaltvCtZ0g.png)

그 다음 터미널을 열어서 프로젝트 디렉토리로 이동하여 다음과 같이 입력을 하여, Github에 Commit을 시켜주고 Push까지 하면 끝납니다.

```

$ git init
$ git config --global user.email "[My Mail Address]"
$ git config --global user.name "[My Name]"
$ git remote add origin [My Repository Address] # 위에서 Github사이트에서 복사해온 주소
$ git remote -v # 잘 적용이 되었는지 확인
$ git add * -f
$ git commit -m "init"
$ git push origin master --force
```

![](https://cdn-images-1.medium.com/max/2000/1*vmVpuhP3aImv5E1IhT9FVQ.png)
![](https://cdn-images-1.medium.com/max/2000/1*FjeCNu9RTB1_aUweuuVzpA.png)
