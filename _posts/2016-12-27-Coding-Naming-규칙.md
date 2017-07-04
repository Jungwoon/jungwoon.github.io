---
layout: post
title: "Coding Naming 규칙"
image: '/assets/img/'
description: '깔끔한 코딩을 위한 부분'
tags:
- Coding
- Tips
categories:
- Tips
---

### 공통 규칙


* 파스칼 표기법(PascalCase)과 카멜 표기법(camelCase)를 사용한다.
    * 파스칼 표기법 : 모든 단어에서 첫번째 문자는 대문자이며 나머지는 소문자이다.(예: PascalCase)
    * 카멜 표기법 : 최초에 사용된 단어를 제외한 첫번째 문자가 대문자이며 나머지는 소문자이다.(예: camelCase)
* 명칭에 약어는 최대한 사용하지않도록 한다.(단, 일반적으로 사용되는 약어는 그대로 사용한다)
* 반의어는 반드시 대응하는 개념으로 사용해야 한다.
    * get / set
    * add / remove
    * create / destroy
    * start / stop
    * insert / delete
    * increment / decrement
    * old / new
    * begin / end
    * first / last
    * up / down
    * min / max
    * next / previous
    * open / close
    * show / hide
    * suspend / resume
    * parent / child


#### 패키지(Package) 명명 규칙


* 패키지명은 표준 패턴을 따라야 한다.
    * [com].[회사명].[프로젝트명].[최상위패키지명].[하위패키지명]
* 패키지명으로는 가급적 한 단어의 명사를 사용한다.
    * 좋은 예 : com.company.project.member.object
    * 나쁜 예 : project.memberObject


#### 클래스(Class) 명명 규칙


* 클래스명에는 파스칼 표기법을 사용한다.
    * public class HelloWorld { }
* 인터페이스에는 특별한 접두사나 접미사를 사용하지않고 파스칼 표기법을 사용한다.
    * public interface Animal { }
* 인터페이스를 구현한 클래스에는 특별한 접두사나 접미사를 사용하지않고 파스칼 표기법을 사용한다.
    * public class Tiger implements Animal { }
* 추상클래스에는 특별한 접두사나 접미사를 사용하지않고 파스칼 표기법을 사용한다.
    * public abstract class Animal { }


#### 메소드(Method) 명명 규칙

* 메소드명에는 파스칼 표기법을 사용한다.
    * public void SayHello(string name) { }
* 속성(attribute)에 접근하는 메소드명의 접두사는 ‘get’, ‘set’를 사용한다.
* 데이터를 조회하는 메소드명의 접두사는 ‘find’을 사용한다.
    * public String findMember(String memberId) { }
* 데이터를 입력하는 메소드명의 접두사는 ‘input’을 사용한다.
    * public int inputMember(HashMap member) { }
* 데이터를 변경하는 메소드명의 접두사는 ‘modify’을 사용한다.
    * public int modifyMember(HashMap member) { }
* 데이터를 삭제하는 메소드명의 접두사는 ‘delete’을 사용한다.
    * public int deleteMember(String memberId) { }
* 초기화하는 메소드명의 접두사는 ‘init’을 사용한다.
    * public String initPassword(String memberId) { }
* 반환값의 타입이 boolean인 메소드는 접두사로 ‘is’를 사용한다.
    * public boolean isMember(String memberId) { }


#### 변수(Variable) 명명 규칙

* 변수와 메소드의 파라미터에는 카멜 표기법을 사용한다.
* 변수명에 헝가리안 표기법을 사용하지 않는다.
    * 헝가리안 표기법(Hungarian Notation) : 변수명을 지을때 한 단어 혹은 두 단어정도로 된 접두사를 사용하여 변수의 데이터 타입이나 자료형등의 성질이나 특성을 나타내는 방법이다.(예 : nHeight, strName, bStopYn…)
    * 예전에는 많이 사용되었으나 현재는 추천하지 않는 편이다.
* 변수에 모든 의미를 충분히 담는다.(약어의 사용을 자제)
    * 좋은 예 : String address; / int salary;
    * 나쁜 예 : String nam; / String addr; / int sal;
* 한 글자로 된 이름을 사용하지 않는다.
* 반복문에서 인덱스로 사용할 변수는 i, j, k… 등으로 사용한다.
* 지역변수와 멤버변수(전역변수)는 변수명 앞에 밑줄(_)을 사용하여 구별한다.(멤버변수에 사용)
* 프로그래밍언어에서 미리 지정한 키워드와 비슷한 이름은 사용하지 않는다.
* boolean 타입의 변수는 접두사로 ‘is’를 사용한다. (예 : boolean isFinished;)
* UI 구성요소를 나타내는 변수는 해당 요소 타입 이름을 접미어로 사용한다.
* UI 구성요소에 적당한 접두사를 사용한다.


#### 그 외의 규칙
* 파일명은 파스칼 표기법을 사용한다.
* 지시자(operator)와 괄호 앞뒤로는 한 칸의 공간을 남긴다
    * 좋은 예 : if ( showResult == true )
    * 나쁜 예 : if(showResult==true)
* 리소스 ID 에는 스네이크 표기법를 사용한다.(예 : snake_case)
*  UI 구성요소를 위하여 공통적인 접두사를 사용한다.
    * Label : lbl
    * TextView : txt
    * DataGrid : dtg
    * Button : btn
    * ImageButton : imb
    * Hyperlink : hlk
    * DropDownList : ddl
    * Image : img
    * Panel : pnl
    * PlaceHolder : phd
    * Table : tbl
* 논리적인 코드 그룹은 빈 줄 하나로 구분해야 한다.
* 무한 루프는 while (true) 형식을 사용한다.
    * 나쁜 예 : for (;;)
    * 나쁜 예 : while (1)
