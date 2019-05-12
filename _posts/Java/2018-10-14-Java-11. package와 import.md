---
title: "[Java] # 1 package와 import"
excerpt: "최신 업데이트 날짜: 2019-05-12"
date: 2019-05-12 17:54:00
categories:
  - Language
tags:
  - Language_Java
search: true
toc: true
sidebar_main: true
---

# 패키지(package)
패키지란, **클래스의 묶음** 으로서 서로 관련된 클래스 및 인터페이스들끼리 그룹 단위로 묶는 것을 말한다. 따라서 클래스의 실제 이름(full name)은 패키지명을 포함한 것이다. 클래스가 물리적으로 하나의 클래스 파일(.class)인 것과 같이 패키지는 물리적으로 하나의 디렉토리이다.

## 패키지의 특징
- 같은 이름의 클래스 일지라도 서로 다른 패키지에 존재하는 것이 가능하다.
  - 자신만의 패키지 체계를 유지할 수 있으며, 다른 개발자가 개발한 라이브러리의 클래스 이름과 충돌하는 것을 피할 수 있다. (C++에서 네임스페이스와 유사하다.)
- 하나의 소스파일에는 첫 번째 문장으로 단 한 번의 패키지 선언만을 허용한다.
- 모든 클래스는 반드시 하나의 클래스에 속해야 한다.
  - 패키지를 선언하지 않으면 **이름없는 패키지(unnamed package)** 에 속하게 된다.
- 패키지는 점(.)을 구분자로 하여 계층 구조로 구성할 수 있다.
  - 예를 들어, String 클래스의 실제 이름은 java.lang.String이다.
- 패키지는 물리적으로 클래스 파일(.class)을 포함하는 하나의 디렉토리이다.

## 패키지 선언

```java
package 패키지명;
```

패키지 선언문은 클래스나 인터페이스의 소스파일(.java)에서 반드시 주석과 공백을 제외한 **첫 번째 문장에 선언해야 한다.** 그리고 하나의 소스파일에 단 한번만 선언될 수 있으며, 해당 소스파일에 포함된 모든 클래스나 인터페이스는 선언된 패키지에 속하게 된다. 패키지명은 대소문자를 모두 허용하지만, 클래스명과 구분하기 위해 일반적으로 **소문자** 로 하는 것을 원칙으로 한다.

## 이름없는 패키지(Unnamed Package)
이름없는 패키지는 default package, anonymous package 등으로도 불리며, 패키지를 선언하지 않는 상태이다. 소스파일에서 패키지를 선언하지 않으면 오류가 아닌 이름없는 패키지에 속하게 된다. 다른 패키지에서 이름없는 패키지를 import할 수 없지만, 이름없는 패키지에서는 다른 패키지를 import할 수 있다.(import는 아래에서 살펴볼 예정이다.)

## 새로운 패키지 생성 순서
- 패키지 예제

___예제 코드___

```java
package com.javachobo.book;

class PackageTest {
	public static void main(String args[]) {
		System.out.println("Hello World!");
	}
}
```

- 컴파일: ``` C:\Program Files\Java\jdk1.8.0_171\work>javac -d . PackageTest.java```
	- '-d' 옵션
	1. 소스파일에 지정된 경로를 통해 패키지의 위치를 찾아서 클래스 파일을 생성한다. (일치하는 디렉토리가 없다면 자동생성)
	2. '-d'옵션 뒤에는 해당 패키지의 루트(root)디렉토리의 경로를 적어준다.
	3. '-d'옵션을 사용하지 않으면, 프로그래머가 직접 계층구조를 만들어야 한다.
- 해당 패키지의 루트 디렉토리를 클래스패스(classpath)에 포함시킨다.
	- 클래스패스는 컴파일러(javac.exe)나 JVM 등이 클래스의 위치를 찾는데 사용되는 경로이다.
	- com.javachobo.book 패키지의 루트 디렉토리는 'com'의 상위 디렉토리인 'C:\Program Files\Java\jdk1.8.0_171\work'이다.
	- '제어판 - 시스템 - 고급 시스템 설정 - 환경변수 - 새로 만들기'를 하면 '새 시스템 변수' 팝업창이 뜬다.
		- 변수 이름: CLASSPATH
		- 변수 값: .;C:\Program Files\Java\jdk1.8.0_171\work;
	- 클래스패스를 지정해주지 않으면 기본적으로 현재 디렉토리(.)로 지정되지만, 클래스패스를 따로 지정해주는 경우에는 직접 추가해주어야 한다.
	- 클래스패스 확인 명령어: ```C:...>echo %classpath%```
- 실행 결과

```
C:/...>java com.javachobo.book.PackageTest
Hello World!
```

- 위의 방법으로 클래스패스를 이용하면, 클래스패스를 따로 설정하지 않아도 된다.
	- 새로 추가하고자 하는 클래스는 'JDK설치디렉토리\jre\classes'디렉토리에 추가한다.
		- classes디렉토리는 사용자가 직접 생성해야한다.
	- jar파일은 'JDK설치디렉토리\jre\lib\ext'디렉토리에 추가한다.
- '-cp'옵션을 이용해서 일시적으로 클래스패스를 지정할 수도 있다. ``` C:...>java -cp C:\Program Files\Java\jdk1.8.0_171\work com.javachobo.book.PackageTest```

# import문
import문은 컴파일러에게 소스파일에 사용된 클래스의 패키지에 대한 정보를 제공한다. 이를 통해 소스코드에 사용되는 **클래스이름에서 패키지명을 생략할 수 있다.** 컴파일러는 import문을 통해 소스파일에 사용된 클래스들의 패키지를 알아낸 다음, 모든 클래스 이름 앞에 패키지명을 붙여준다. 이는 **프로그램의 성능에 전혀 영향을 미치지 않으며,** 컴파일 시간이 아주 조금 더 걸릴 뿐이다.

import문의 위치는 모든 소스파일(.java)에서 **package문 다음, 클래스 선언문 이전에** 위치해야 한다. 한 소스파일에서 여러 번의 import문이 사용될 수 있다. 따라서 일반적으로 소스파일의 구성은 package문, import문, class 순으로 구성되어 있다.

## import문 선언

```java
import 패키지명.클래스명;
import 패키지명.*;
```

import문은 위와 같이 사용될 수 있다. 그리고 동일한 패키지의 모든 클래스를 포함하고 싶다면 와일드카드(*) 문자를 사용할 수 있다. 단, 주의할 점은 하위 패키지를 대신해서 와일드카드 문자는 사용할 수 없다.

```java
import java.util.*;
import java.text.*;
//import java.*;로 대신할 수 없다!
```

위 예제는 ```java.util``` 패키지와 ```java.test``` 패키지에 와일드카드 문자를 사용하여 여기에 포함된 모든 클래스를 import한 것이다. 하지만 상위 패키지 ```java.*``` 으로 하위 패키지는 포함시킬 수 없다.

자바는 기본적으로 ```java.lang``` 패키지를 포함하고 있기 때문에 String과 같은 클래스를 import문 없이 사용할 수 있다.

## static import문
static import문은 정적 메소드를 import하는 것으로 이를 통해 static 멤버를 호출할 때 클래스 이름을 생략할 수 있다.

```java
import static java.lang.Integer.*;     // Integer클래스의 모든 static 메서드
import static java.lang.Math.random;   // Math.random()만 . 괄호 안붙임
import static java.lang.System.out;    // System.out을 out만으로 참조가능
```

위와 같이 선언하였다면, 아래와 같이 클래스 이름을 생략할 수 있다.

```java
System.out.println(Math.random());   // 생략 전
out.println(random());               // 생략 후
```

static import문은 **매우 드물게** 사용된다. 그리고 이를 남용하면 코드를 매우 읽기 힘들게 하므로 유지보수하기 어렵다. 그러므로 다음과 같은 경우가 아닐 때는 static import문 사용을 지양해야 한다. 이를 사용하는 경우는 한 두개의 클래스에서 정적 멤버를 빈번하게 접근해야 할 때 이용한다.(이 부분은 좀 더 알아봐야 할 듯 하다.)
