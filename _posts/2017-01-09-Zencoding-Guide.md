---
layout: post
title: "Zencoding(=Emmet) 사용법"
image: '/assets/img/'
description: 'Zencoding을 통해 생산성 높이기'
tags:
- HTML
- Zencoding
categories:
- Tips
---

젠코딩을 통해서 생산성을 높이는 방법에 대해서 알아보도록 하겠습니다.

### Grammer
- ID, CLASS 속성 : ID - ```#```, CLASS - ```.```
	- div#page.section.main
	- ```<div id="page" class="section main"></div>```

- 커스텀 속성 : []로 묶어서 안에 속성을 넣어준다.
	- a[title="hello world"]
	- ```<a title="hello world"></a>```

- 엘리먼트 복사 : *
	- li*3
	- ```<li></li><li></li><li></li>```

- 넘버링 : $ 위치에 값이 1부터 1씩 증가 (1,2,3,4...)
	- li.item$*3
	- ```<li class="item1"></li><li class="item2"></li><li class="item3"></li>```
	- li.item$$$*2
	- ```<li class="item001"></li><li class="item002"></li>```

- 그룹핑 : ()를 이용해서 그룹을 감싸준다.
	- div#page>(div#header>ul#nav>li*4>a)+(div#page>(h1>span)+p*2)+div#footer

- ID, CLASS 앞에 태그명을 생략하면 DIV로 간주
	- .container>#section
	- ```<div class="container"><div id="section"></div></div>```

- 텍스트 : {}를 이용하여 안쪽에 텍스트를 넣어준다.
	- p>{click}
	- ``` <p>click</p>```

> 참조 :
> https://code.google.com/p/zen-coding/

---

### Example

html:xt

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
<head>
    <meta http-equiv="Content-Type" content="text/html;charset=UTF-8">
    <title>Document</title>
</head>
<body>

</body>
</html>
```


h${h$. Bootstrap heading}*6

```
<h1>h1. Bootstrap heading</h1>
   <h2>h2. Bootstrap heading</h2>
   <h3>h3. Bootstrap heading</h3>
   <h4>h4. Bootstrap heading</h4>
   <h5>h5. Bootstrap heading</h5>
   <h6>h6. Bootstrap heading</h6>
```


(h${h$. Bootstrap heading}>small{Secondary text})*6

```
<h1>h1. Bootstrap heading<small>Secondary text</small></h1>
   <h2>h2. Bootstrap heading<small>Secondary text</small></h2>
   <h3>h3. Bootstrap heading<small>Secondary text</small></h3>
   <h4>h4. Bootstrap heading<small>Secondary text</small></h4>
   <h5>h5. Bootstrap heading<small>Secondary text</small></h5>
   <h6>h6. Bootstrap heading<small>Secondary text</small></h6>
```

div.row>div.col-md-4*3{.col-md-4}*3

```
<div class="row">
   <div class="col-md-4">.col-md-4</div>
   <div class="col-md-4">.col-md-4</div>
   <div class="col-md-4">.col-md-4</div>
</div>
```
