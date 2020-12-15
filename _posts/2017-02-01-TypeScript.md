---
layout: post
title: "TypeScript 기본 문법"
image: '/assets/img/'
description: 'Angular2를 위한 TypeScript'
tags:
- TypeScript
- Angular2
categories:
- Web
- Angular2
- TypeScript
---

이번에는 타입스크립트의 기본적인 문법을 알아보도록 하겠습니다.

#### 조심
*.ts 파일을 tsc 명령어로 *.js로 변환후에 node를 실행시켜야 함


#### var

```typescript
var emotion="happy"
{
	var emotion="sad";
}
console.log(emotion);

// Result
sad
```


#### let

```typescript
var emotion2="sad";

{
	let emotion2 : string = "happy";
	console.log(emotion2);
}
console.log(emotion2);

// Result
happy
```


#### Array

```typescript
var fruits2 : number[][] = [
    [1,0,0],
    [0,3,0],
    [0,0,4],
];

for (var i=0; i<3; i++) {
    for (var j=0; j<3; j++) {
        console.log(fruits2[i][j]);
    }
    console.log("");
}

console.log(fruits2[0][0]*fruits2[1][1]*fruits2[2][2]);

// Result
12
```

#### Union Type : 결과값에 의한 타입추론

```typescript
var unionX : string | number | boolean = false;
var unionY : boolean | string = "hello";

console.log(typeof unionX, unionX);
console.log(typeof unionY, unionY);

// Result
boolean false
string hello
```

#### 문자열 표현

Tips) ' 와 "를 구분하지 않음

` 안에 문자열을 넣으면 입력한 그대로 변환되어서 나온다

Example)

```typescript
let multi_line : string = `
    one = 1
    two = 2
`

console.log(multi_line);

// Result
	one = 1
	two = 2

```

#### 디스트럭처링 지원

```typescript
var params2 = ['happy 동물원', 100];
let [m_name2, m_num2] = params2;

console.log(`
    이름 : ${m_name2}
    만족도 : ${m_num2}
`);

// Result
	이름 : happy 동물원
	만족도 : 100

```

#### 배열 메서드

Array.isArray()

Array.prototype.every()

Array.prototype.filter()

Array.prototype.forEach()

```typescript
function printArray(element, index, array) {
	console.log('[' + index + '] = ' + element);
}

[1, 2, 3, 'four'].forEach(printArray);

// result
[0] = 1
[1] = 2
[2] = 3
[3] = 'four'
```


#### 람다함수

```typescript
var pow3 = (x) => {
    return x * x;
}

console.log(pow3(3));

console.log(((x) => {
    return x * x;
})(4));

((x) => {
    return x * x;
})(5);

// Result
9
16
25

```


#### getter, setter

```typescript
var obj = {
    msg: '',

    get value() : string {
        return this.msg + " world";
    },

    set value(v : string) {
        this.msg = v;
    }
}

obj.value = "Hello";
console.log(obj.value);

// result
Hello World

```

#### 클래스 선언과 사용

```typescript
class Car {
    _brand : string;
    _numTier : number;
    _carName : string;

    constructor(brand : string, carName : string, numTier : number) {
        this._brand = brand;
        this._carName = carName;
        this._numTier = numTier;
    }
}

class MyCar extends Car {

    constructor(brand : string, carName : string, numTier : number) {
        super(brand, carName, numTier);
    }

    get getCarName() : string {
        return this._carName;
    }

    get getNumTier() {
        return this._numTier;
    }
}

let my : MyCar = new MyCar("KIA", "Happy", 4);
console.log(my.getCarName + " 자동차의 타이어 개수 : " + my._numTier);

```

#### Interface

```typescript
interface Car {
    setTier(tier : number);

    getTier();

    setCarName(name : string);

    getCarName();
}

class CarImpl implements Car {
    _tier : number;
    _name : string;

    setTier(tier : number) {
        this._tier = tier
    }

    getTier() {
        return this._tier;
    }

    setCarName(name : string) {
        this._name = name;
    }

    getCarName() {
        return this._name;
    }
}
```

#### Abstract Class

```typescript
abstract class SmallAnimals {
    abstract sound(): string;

    abstract name() : string;

    makeSound() : string {
        return `${this.name()} : ${this.sound()} `
    }
}

class Mouse extends SmallAnimals {
    sound() : string {
        return "peep peep~";
    }

    name() : string {
        return "mouse";
    }
}

var mouse = new Mouse();
console.log(mouse.makeSound);
```
