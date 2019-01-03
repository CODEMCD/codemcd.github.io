---
title: "[jsdsLib]Data Structures Visualizations"
excerpt: "자료구조 시각화 오픈소스 튜토리얼"
categories:
  - Toy
tags:
  - Toy_jsdsLib
search: true
sidebar_main: true
toc: true
---

이 문서는 오픈소스 Data Structures Visualizations 튜토리얼 부분을 번역한 것입니다.

>> 오타, 잘못된 번역이 많을 수 있습니다. 감안하고 읽어주세요...

원문
- [https://www.cs.usfca.edu/~galles/visualization/source.html](https://www.cs.usfca.edu/~galles/visualization/source.html)

## Source Code
주의사항
1. 이 소스 코드를 보고 알고리즘을 이해할려고 하지 마십시오. 이 소프트웨어는 알고리즘을 시각화하는 프로퍼티를 만들기 위해 한 두개의 이해하기 힘든 알고리즘을 구현하였습니다. 적절한 소스 코드를 얻을려면 favorte textbook이나 위키디피아를 참고할 것을 추천합니다.
2. 다른 모든 소프트웨어들과 같이 이 프로젝트는 주기적으로 업데이트를 하고 있습니다. 이 프로젝트은 자바 프로젝트로 시작하여, ActionScript3(flash)로 다시 구현되었고, 자바스크립트에 이식하였습니다. 이는 플래시와 자바스크립트를 공부할 수 있는 기회였고, 시간이 지나면서 이미 이 소프트웨어가 반 이상 진행된 후였습니다. 현재까지 코드를 최적화하고 있지만 아직까지 최적화해야할 곳이 어느정도 많이 남아있습니다. 다음 버전에서는 더 발전해 있을 것입니다.

## Visualization Creation Tutorial
새로운 시각화를 만들기 위해서는 자바스크립트(.js)파일과 HTML(.html) 파일이 필요합니다. HTML 파일은 한 템플릿이 필요하고, 한 두가지 정도 변경되어야 합니다(자바스크립트 파일 이름과 같은 것들). HTML 템플릿의 예제들과 이것을 어떻게 변경할 지에 대해서는 튜토리얼 마지막에 있습니다. 자바스크립트 파일에서는 다음의 기능을하는 function 을 생성합니다.

1. 시각화를 조정할 적절한 control 들을 만든다.(element 를 삽입하거나 삭제하는 기능 등)
2. 시각화를 구현하는 control 들을 위한 콜백 함수를 만든다. 이 시각화는 애니메이션 관리자에게 문자열 배열을 보냄으로서 구현된다. 이 애니메이션 관리자는 애니메이션을 구현할 것이고, 모든 애니메이션 control 을 관리한다.
3. 애니메이션 관리자로부터 undo 이벤트를 위한 listen 함수를 실행하며, undo 이벤트가 감지되면 마지막 작업을 되돌린다. (roll back)

### Using Algorithm function
해당 라이브러리를 사용하더라도 자바스크립트 function 을 만드는 것은 꽤 복잡합니다. 만약 만들고자 하는 함수가 이 Algorithm 함수(AlgorithmLibrary/Algorithm.js) 하위 클래스로 만든다면, 많은 복잡한 세부사항들을 자동화할 수 있습니다. 압축 파일에 포함되어 있는 "MyAlgorithm"(AlgorithmLibrary/MyAlgorithm.js) 함수들을 기반으로 사용할 수 있습니다.

이 프로젝트는 FreeBSD 라이센스를 따릅니다. 모든 코드 파일에는 아래와 같은 라이선스 세부사항이 표시되어 있습니다.

```
// Copyright 2011 David Galles, University of San Francisco. All rights reserved.
//
// Redistribution and use in source and binary forms, with or without modification, are
// permitted provided that the following conditions are met:
//
// 1. Redistributions of source code must retain the above copyright notice, this list of
// conditions and the following disclaimer.
//
// 2. Redistributions in binary form must reproduce the above copyright notice, this list
// of conditions and the following disclaimer in the documentation and/or other materials
// provided with the distribution.
//
// THIS SOFTWARE IS PROVIDED BY David Galles ``AS IS'' AND ANY EXPRESS OR IMPLIED
// WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND
// FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL  OR
// CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR
// CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
// SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON
// ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING
// NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF
// ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
//
// The views and conclusions contained in the software and documentation are those of the
// authors and should not be interpreted as representing official policies, either expressed
// or implied, of the University of San Francisco
```

다음은 알고리즘 정의입니다. 자바스크립트 내에서 일종의 가짜(faked) 상속을 사용하고 있습니다. 이 프로젝트의 함수를 다음과 같이 정의합니다.

1. 함수 프로토타입은 슈퍼 클래스의 프로토타입으로 설정한다.
2. 생성자를 우리 자신의 생성자로 재설정한다.
3. 슈퍼 클래스 프로토타입을 캐시한다.

위는 자바의 'super' 호출과 같은 기능을 구현하기 위함입니다. 이렇게 상속을 구현한 것은 생성자가 정상적으로 동작하기 위한 것입니다. 그리고 메인 생성자 함수가 init 함수를 호출하는 것 외에는 어떠한 기능도 하지 않습니다.(이 방법은 슈퍼 클래스의 init 함수를 호출할 수도 있습니다. 하지만 이는 보안적으로 매우 위험합니다.)

```js
function MyAlgorithm(am, w, h)
{
	this.init(am, w, h);
}

MyAlgorithm.prototype = new Algorithm();
MyAlgorithm.prototype.constructor = MyAlgorithm;
MyAlgorithm.superclass = Algorithm.prototype;
```

각 함수는 자신의 생성자를 가지고 있습니다. 일반적으로, 각 자신의 생성자에서 다음과 같은 것들이 필요할 것이라고 예상합니다.

- 슈퍼 클래스 생성자를 호출한다. 자바스크립트 문법에 따르면, 이것은 약간 이상할 수 있습니다. 그러나 이 프로젝트는 자바스크립트의 전통적인 객체 지향 패러다임에 지향하고 있지만, 이것이 구현에서 조금 어렵고 복잡할 수 있습니다.
- 필요한 컨트롤러를 추가한다.
- 메모리 매니저를 초기화한다. 대부분의 경우에는 매우 간단한 메모리 매니저를 사용할 것입니다. -- 오래된 파스칼 스타일의 "Never free" 메모리 매니저입니다. 리스트는 0부터 시작해서 새로운 메모리가 필요할 때마다 증가시킵니다.
- 사용하게 될 자료 구조를 초기화 한다.

```js
MyAlgorithm.prototype.init = function(am, w, h)
{
	// Call the unit function of our "superclass", which adds a couple of
	// listeners, and sets up the undo stack
	MyAlgorithm.superclass.init.call(this, am, w, h);

	this.addControls();

	// Useful for memory management
	this.nextIndex = 0;

	// TODO:  Add any code necessary to set up your own algorithm.  Initialize data
	// structures, etc.
}
```

control 을 추가하는 함수가 있습니다. 여기에는 control 을 추가하는 helper 함수 역시 몇 가지 있습니다. Algorithm.js 파일을 보면 helper 함수에 대한 정보를 알 수 있습니다.

```js
MyAlgorithm.prototype.addControls =  function()
{
	this.controls = [];

    // Add any necessary controls for your algorithm.
    //   There are libraries that help with text entry, buttons, check boxes, radio groups
    //
    // To add a button myButton:
    //     this.mybytton = addControlToAlgorithmBar("Button", "MyButtonText");
    //     this.mybytton.onclick = this.myCallback.bind(this);
    //     this.controls.push(this.mybutton);
    //   where myCallback is a method on this function that implemnts the callback
    //
    // To add a text field myField:
    //    this.myField = addControlToAlgorithmBar("Text", "");
    //    this.myField.onkeydown = this.returnSubmit(this.myField,  
    //                                               this.anotherCallback.bind(this), // callback to make when return is pressed
    //                                               maxFieldLen,                     // integer, max number of characters allowed in field
    //                                               intOnly);                        // boolean, true of only digits can be entered.
    //    this.controls.push(this.myField);
    //
    // To add a textbox:
    //   	this.myCheckbox = addCheckboxToAlgorithmBar("Checkbox Label");
    //      this.myCheckbox.onclick = this.checkboxCallback.bind(this);
    //      this.controls.push(myCheckbox);
    //
    // To add a radio button group:
    //	  this.radioButtonList = addRadioButtonGroupToAlgorithmBar(["radio button label 1",
    //                                                              "radio button label 2",
    //                                                              "radio button label 3"],
    //                                                             "MyButtonGroupName");
    //    this.radioButtonList[0].onclick = this.firstRadioButtonCallback.bind(this);
    //    this.controls.push(this.radioButtonList[0]);
    //    this.radioButtonList[1].onclick = this.secondRadioButtonCallback.bind(this);
    //    this.controls.push(this.radioButtonList[1]);
    //    this.radioButtonList[2].onclick = this.thirdRadioButtonCallback.bind(this);
    //    this.controls.push(this.radioButtonList[1]);
    //
    // Note that we are adding the controls to the controls array so that they can be enabled / disabled
    // by the animation manager (see enableUI / disableUI below)
}
```

매서드를 리셋하기 위한 "override" 가 필요합니다. 애니메이션 매니저가 undo 작업을 할 때 마다 아래와 같은 작업을 수행합니다.

- 이 리셋 매서드가 호출된다. 이 매서드는 해당 오브젝트의 상태를 리셋합니다. 정확히는 해당 작업이 수행되기 전으로 되돌립니다.
- 마지막에 진행되었던 모든 작업을 포함한다.(애니메이션 정보가 삭제되어도 상관없습니다.)
- 우리가 수행했던 마지막 작업 바로 전의 상태로 되돌리고 종료된다.

```js
MyAlgorithm.prototype.reset = function()
{
	// Reset all of your data structures to *exactly* the state they have immediately after the init
	// function is called.  This method is called whenever an "undo" is performed.  Your data
	// structures are completely cleaned, and then all of the actions *up to but not including* the
	// last action are then redone.  If you implement all of your actions through the "implementAction"
	// method below, then all of this work is done for you in the Animation "superclass"

	// Reset the (very simple) memory manager
	this.nextIndex = 0;
}
```

- 콜백 함수

```js
//////////////////////////////////////////////
// Callbacks:
//////////////////////////////////////////////
//
//   All of your callbacks should *not* do any work directly, but instead should go through the
//   implement action command.  That way, undos are handled by ths system "behind the scenes"
//
//   A typical example:
//
//MyAlgorithm.prototype.insertCallback = function(event)
//{
//	// Get value to insert from textfield (created in addControls above)
//	var insertedValue = this.insertField.value;
//
//  // If you want numbers to all have leading zeroes, you can add them like this:
//	insertedValue = this.normalizeNumber(insertedValue, 4);
//
//  // Only do insertion if the text field is not empty ...
//	if (insertedValue != "")
//	{
//		// Clear text field after operation
//		this.insertField.value = "";
//      // Do the actual work.  The function implementAction is defined in the algorithm superclass
//		this.implementAction(this.insertElement.bind(this), insertedValue);
//	}
//}
//  Note that implementAction takes as parameters a function and an argument, and then calls that
//  function using that argument (while also storing the function/argument pair for future undos)

//////////////////////////////////////////////
// Doing actual work
//////////////////////////////////////////////
//   The functions that are called by implementAction (like insertElement in the comments above) need to:
//
//      1. Create an array of strings that represent commands to give to the animation manager
//      2. Return this array of commands
//
//    We strongly recommend that you use the this.cmd function, which is a handy utility function that
//    appends commands onto the instance variable this.commands
//
//    A simple example:
//
//MyAlgorithm.simpleAction(input)
//{
//	this.commands = [];  // Empty out our commands variable, so it isn't corrupted by previous actions
//
//	// Get a new memory ID for the circle that we are going to create
//	var circleID = nextIndex++;
//	var circleX = 50;
//	var circleY = 50;
//
//	// Create a circle
//	this.cmd("CreateCircle", circleID, "Label",  circleX, circleY);
//	circleX = 100;
//	// Move the circle
//	this.cmd("Move", circleID, circleX, circleY);
//	// First Animation step done
//	this.cmd("Step");
//	circleX = 50;
//	circleY = 100;
//	// Move the circle again
//	this.cmd("Move", circleID, circleX, circleY);
//	// Next Animation step done
//	this.cmd("Step");
//	// Return the commands that were generated by the "cmd" calls:
//	return this.commands;
//}
```

- UI 사용/사용안함

```js
// Called by our superclass when we get an animation started event -- need to wait for the
// event to finish before we start doing anything
MyAlgorithm.prototype.disableUI = function(event)
{
	for (var i = 0; i < this.controls.length; i++)
	{
		this.controls[i].disabled = true;
	}
}

// Called by our superclass when we get an animation completed event -- we can
/// now interact again.
MyAlgorithm.prototype.enableUI = function(event)
{
	for (var i = 0; i < this.controls.length; i++)
	{
		this.controls[i].disabled = true;
	}
}
```

- 시작 스크립트

```js
////////////////////////////////////////////////////////////
// Script to start up your function, called from the webapge:
////////////////////////////////////////////////////////////

var currentAlg;

function init()
{
	var animManag = initCanvas();
	currentAlg = new MyAlgorithm(animManag, canvas.width, canvas.height);

}
```

## Animation Commands
사용자가 애니메이션 매니저에게 전달하는 명령어는 문자열 리스트(배열) 입니다. 각 문자열은 명령어의 이름(대소문자 구분 안 함)과 함께 ```<;>``` 토큰으로 구분된 매개변수 리스트로 시작합니다. 명령어의 첫 번째 매개변수의 대부분은 생성하거나 접근할려는 오브젝트의 ID 입니다. 예를들어, 오브젝트 ID 37 을 (100, 200) 위치로 이동하는 문자열은 아래와 같습니다.
```
"Move<;>37<;>100<;>120"
```
명령어는 두 그륩으로 분리됩니다.

- 애니메이션 오브젝트를 만드는 명령어
- 이미 만들어진 오브젝트를 조작하는 명령어

### Object Creation and Deletion Commands
오브젝트 생성 명령어의 첫 번째 매개변수는 생성할 오브젝트의 인덱스를 나타내는 정수를 사용합니다. 이 정수 번호는 현재 시스템에서 활성화중인 다른 오브젝트 번호와 중복되어선 안됩니다.(오브젝트가 삭제가 된다면 삭제된 오브젝트의 번호는 재사용을 할 수 있습니다.) 해당 명령어 역시 다른 모든 명령어와 같이, 필수 매개변수와 옵션 매개변수를 가집니다.

- CreateCircle: objectID, label, [initial_x, initial_y]
	- objectID: ID 번호는 음수가 아닌 정수여야 하고, 다른 활성화된 ID 번호와 중복이 되어서는 안됩니다. 그리고 성능을 위해 가능한 ID 번호의 크기가 작은 것이 좋습니다.
	- label: 원에 나타날 데이터입니다. 이 데이터는 원 중앙에 표시되고, 문자열이라면 개행 문자(\n)를 사용할 수 있습니다.
	- initial_x: (optional, defaults to 0) 원의 초기 x 좌표
	- initial_y: (optional, defaults to 0) 원의 초기 y 좌표
- CreateRectange: objectID, label, width, height, [initial_x, initial_y, xJustify, yJustufy, backgroundColor, foregroundColor]
	- objectID: ID 번호는 음수가 아닌 정수여야 하고, 다른 활성화된 ID 번호와 중복이 되어서는 안됩니다. 그리고 성능을 위해 가능한 ID 번호의 크기가 작은 것이 좋습니다.
	- label: 사각형에 나타날 데이터입니다. 이 데이터는 사각형 중앙에 표시되고, 문자열이라면 개행 문자(\n)를 사용할 수 있습니다.
	- width: 사각형의 넓이(pixel 단위)
	- height: 사각형의 높이(pixel 단위)
	- initial_x: (optional, defaults to 0) 사각형의 초기 x 좌표
	- initial_y: (optional, defaults to 0) 사각형의 초기 y 좌표
	- xJustify: (optional, defaults to "center") 중앙, 오른쪽, 왼쪽 중 위치를 선택할 수 있고 만약 사각형 위치가 (x, y) 이라면, x 는 선택된 위치에 나타납니다.
	- yJustify: (optional, defaults to "center"). 중앙, 위, 아래 중 위치를 선택할 수 있고 만약 사각형 위치가 (x, y) 이라면, x 는 선택된 위치에 나타납니다.
	- foregroundColor: HTML 색상의 문자열 표시("빨간색인 경우 #FF0000", 녹색인 경우 "#00FF00" 등)를 사용하여 사각형의 전경색(foreground)을 나타내는 초기 색. 기본값: 검은색
	- backgroundColor: HTML 색상의 문자열 표시("빨간색인 경우 #FF0000", 녹색인 경우 "#00FF00" 등)를 사용하여 사각형의 배경을 나타내는 초기 색. 기본값: 흰색
- CreateHighlightCircle: objectID, color, [initial_x, initial_y, radius]
A highlight circle 은 다른 물체(object) 를 가리지 않기 위해 위해 일반 원과 달리 label과 배경색이 없습니다.
	- objectID: ID 번호는 음수가 아닌 정수여야 하고, 다른 활성화된 ID 번호와 중복이 되어서는 안됩니다. 그리고 성능을 위해 가능한 ID 번호의 크기가 작은 것이 좋습니다.
	- color: HTML 색상 표기를 사용한 원의 초기 색
	- initial_x: (optional, defaults to 0) 원의 초기 x 좌표
	- initial_y: (optional, defaults to 0) 원의 초기 y 좌표
	- radius: (optional, defaults to 20) 원의 반지름
- CreateLabel: objectID, label, [initial_x, initial_x, centered]
	- objectID: ID 번호는 음수가 아닌 정수여야 하고, 다른 활성화된 ID 번호와 중복이 되어서는 안됩니다. 그리고 성능을 위해 가능한 ID 번호의 크기가 작은 것이 좋습니다.
	- label:라벨에 나타날 데이터. 문자열이라면 개행 문자(\n)를 사용할 수 있습니다.
	- initial_x: (optional, defaults to 0) 라벨의 초기 x 좌표
	- initial_y: (optional, defaults to 0) 라벨의 초기 y 좌표
	- centered: (optional, defaults to true) true(1) 값이라면 중앙에, false(0) 값이면 중앙에 있지 않습니다.
- CreateLinkedList: objectID, label, width, height, [initial_x, initial_y, linkPercent, verticalOrientation, linkPosEnd, numLabels]
	- objectID: ID 번호는 음수가 아닌 정수여야 하고, 다른 활성화된 ID 번호와 중복이 되어서는 안됩니다. 그리고 성능을 위해 가능한 ID 번호의 크기가 작은 것이 좋습니다.
	- label: 연결된 리스트 원소 내의 레이블(또는 두 개 이상의 레이블이 있는 경우, 그 중 첫 번째 레이블)
	- width: 연결된 리스트 원소의 넓이(pixel 단위)
	- height: 연결된 리스트 원소의 높이(pixel 단위)
	- initial_x: (optional, defaults to 0) 연결된 리스트 원소의 초기 x 좌표
	- initial_y: (optional, defaults to 0) 연결된 리스트 원소의 초기 y 좌표
	- linkPercent: (optional, defaults to 0.25) The percentage of the linked list element that the outgoing pointer takes up.
	- verticalOrientation: (optional, defaults to true). Should the linked list element be vertial (true) or horizontal (false)
	- linkPosEnd: (optional, defaults to false). Should the poiner appear at the bottom or left of the linked list element (true), or the top or right of the linked list element (false)
	- numLabels: (optional, defaults to 1). The number of labels that the linked lists element can hold. See the adjacency list implementat of a graph visualization for an example of a linked list element with more than 1 label.
- CreateBTreeNode: objectID, widthPerLabel, height, numLabels, inital_x, initial_y, backgroundColor, foregroundColor
Somewhat special-purpose animated object created for B-Trees. Single rectangle containing any number of labels, with no dividing lines between the labels. Edges can be attached between each label, and to the left of the leftmost label, and to the right of the rightmost label. See the BTree and B+ Tree visualizations for examples.
	- objectID: Non-negative integer that represents the ID of this object. Must be different from any ID currently active. Should be as small as posslbe for better performance.
	- widthPerLabel: The width of the B-Tree node is the number of labels * the width per label. Value is in pixels.
	- height: Height of the B-Tree node in pixels
	- numLabels: The number of labels in the BTree node.
	- initial_x: The initial x position of the B-Tree node
	- initial_y: The initial y position of the B-Tree node
	- backgroundColor: The initial color of the background of the rectangle, using HTML colors (#FF0000 for red, #00FF00 for green, and so on)
	- backgroundColor: The initial color of the forground of the rectangle, using HTML colors (#FF0000 for red, #00FF00 for green, and so on)
- Delete: objectID
	- objectID: The ID of the object to delete. All edges incident on this object will be removed. (If the delete is undone, then all such edges will be restored). Once an Animated Element has been deleted, its ID is free to be used again. Note that overly complicated ID management (which is really memory management, since IDs are just indicies into a "memory array" of active animated objects) is not necessarily recommended, since it can lead to some subtle bugs.

현재 사용중인 objectID 를 생성하려고 하거나 현재 존재하지 않는 objectID 를 삭제하려고 하면 오류가 발생한다.

### Object Manipulation Commands

- Move: objectID, toX, toY
Move the object smoothly over the next step from the current position to the new position
	- objectID: The ID of the object to move. The object must exists, or an exception will be thrown
	- toX: The new X location to move to
	- toY: The new Y location to move to
- SetPosition: objectID, toX, toY
Move the object immediately to the new position at the beginning of the next step
	- objectID: The ID of the object to move. The object must exists, or an exception will be thrown
	- toX: The new X location to move to
	- toY: The new Y location to move to
- SetForegroundColor: objectID, color
Sets the foreground color (outline color and label color) of an object. Note that if an object has several labels this will set the color of all labels.
- objectID: The ID of the object to modify. The object must exists, or an exception will be thrown
- color: New foreground color (string representing HTML color, like "#ff0000")
- SetBackgroundColor: objectID, color
Sets the background color of current object. Note that if an object has several labels this will set the color of an object.
	- objectID: The ID of the object to modify. The object must exist, or an exception will be thrown
	- color: New background color
- SetHighlight: objectID, highlightVal
Mark an object as either highlighted or unhighlighted, based on the value of highlightVal. Objects that are highlighted will pulse red. Any object can be highlighted (thought labels are slightly harder to read when highlighted) Note that if an object is left highlighted after an animation is ended, it will not pulse until the animation starts again. Edges can be highlighted using the highlight edge command.
	- objectID: The ID of the object to modify. The object must exists, or an exception will be thrown
	- highlightVal: 1 or true, turn on highlighting. 0 or false, turn off highlighting.
- SetText: objectID, newText, [textIndex]
Sets the value of the label associated with the object (the printing withing a circle, for instance).
	- objectID: The ID of the object to modify. The object must exists, or an exception will be thrown
	- newText: The new text of the label
	- textIndex: (optional, defaults to 0) Index of the text label to change. Only used in objects that have more than one text label (B-Tree nodes, Linked List nodes). If the object does not support multiple labels, this is ignored.
- SetAlpha: objectID
Sets the alpha (transparency) of the object. 0 is completely transparent, 1 is completely opaque. Works for all objects.
	- objectID: The ID of the object to modify. The object must exists, or an exception will be thrown.
- SetHeight: objectID, newHeight
Sets the height (in pixels) of the object.
	- objectID: The ID of the object to modify. The object must exists, or an exception will be thrown
	- newHeight: The new height of the object.
- SetWidth: objectID, newWIdth
Sets the width (in pixels) of the object.
	- objectID: The ID of the object to modify. The object must exists, or an exception will be thrown
	- newWidth: The new width of the object.
- SetTextColor: objectID, newColor, [textIndex]
Sets the color of the label associated with the object
	- objectID: The ID of the object to modify. The object must exists, or an exception will be thrown
	- newColor: The new color to set. As with all colors, should be a html color string
	- textIndex: (optional, defaults to 0) If the object contain multiple labels (such as a linked-list node, or a B-Tree node) determine which label to change the color of. If the object only has one label, this parameter is ignored.
- SetNull: objectID, nullValue
Currently only used for linked-list elements. Should the area set aside for the pointer in the linked list object be drawn as a null pointer (slash through the field)? This should probably be automated (draw the slash if and only if the node is not connected to anything), but for now this must be done manually.
	- objectID: The ID of the object to modify. The object must exists, or an exception will be thrown
	- nullValue: 0 or false for do not draw the slash, 1 or true for draw the slash.
- SetNumElements: objectID, numElements
Currently only used for B-Tree nodes. Changes the number of labels stored in this B-tree node. Should probably be extended to at least Linked-list nodes.
	- objectID: The ID of the object to modify. The object must exists, or an exception will be thrown
	- numElements: integer, the number of elements this B-Tree node should have
- AlignRight: object1ID, object2ID
Align object1 so that it is immediately to the right of object2. Very handy for lining up labels (where you don't necessarily know the width of the label), but can be used with any two objects.
	- object1ID: The ID of the object to move. The object must exists, or an exception will be thrown
	- object2ID: The ID of the object used to align object1. The object must exists, or an exception will be thrown
- AlignLeft: object1ID, object2ID
Align object1 so that it is immediately to the left of object2. Very handy for lining up labels (where you don't necessarily know the width of the label), but can be used with any two objects.
	- object1ID: The ID of the object to move. The object must exists, or an exception will be thrown
	- object2ID: The ID of the object used to align object1. The object must exists, or an exception will be thrown
- AlignTop: object1ID, object2ID
Align object1 so that it is immediately on top of of object2. Very handy for lining up labels (where you don't necessarily know the width of the label), but can be used with any two objects.
	- object1ID: The ID of the object to move. The object must exists, or an exception will be thrown
	- object2ID: The ID of the object used to align object1. The object must exists, or an exception will be thrown
- AlignBottom: object1ID, object2ID
Align object1 so that it is immediately below object2. Very handy for lining up labels (where you don't necessarily know the width of the label), but can be used with any two objects.
	- object1ID: The ID of the object to move. The object must exists, or an exception will be thrown
	- object2ID: The ID of the object used to align object1. The object must exists, or an exception will be thrown

### Edge Manipulation Commands
Edge 명령어는 edge 를 가진 두 연관된 오브젝트를 조작합니다. Edge 들은 그래픽적으로 방향이 있을 수도 없을 수도 있지만, 후드(hood) 아래에 있는 모든 edge 들은 방향을 가집니다. 그리고 이 방향은 edge가 생성될 때 주어집니다. 한 오브젝트에서 다른 오브젝트로 가는 단 하나의 edge 만 있을 수 있습니다.(오브젝트1 에서 오브젝트2 까지와 오브젝트2 에서 오브젝트 1까지 서로 다른 edge 입니다.) Edge 들은 항상 두 오브젝트 ID(시작 오브젝트 ID, 끝 오브젝트 ID) 에 의해 수행됩니다. 오브젝트는 항상 다른 오브젝트와 연결될 수 있습니다.

- Connect: fromID, toID, [linkColor, curve, directed, label, anchorPosition]
	- fromID: The ID of the object at the tail of the new edge
	- toID: The ID of the object at the head of the new edge
	- linkColor: (optional, defaults to "#000000") The color of the edge
	- linkColor: (optional, defaults to false) true for a diected edge, false for an undirected edge
	- curve: (optional, defaults to 0.0) The "curviness" of the edge. 0.0 is perfectly straight, positive values arc to the right, negative values arc to the left.
	- directed: (optional, defaults to true). True if the edge is directed, false if the edge is undirected
	- label: (optional, defaults to ""). The label that appears along the edge (useful for things like edge costs in graphs)
	- anchorPosition: (optional, defaults to 0) If the edge could have more than one attachment postion at the "from" node, (currently only used for B-Tree nodes, but could well be expanded to things like doubly-linked lists) the index of the attachment position to use. Ignored for animated objects that do not have multiple attachment positions
- Disconnect: fromID, toID
Removes an edge between two elements. If there is no edge, then this operation is a no-op.
	- fromID: The ID of the "From" direction of the edge
	- toID: The ID of the "To" direction of the edge
Note that even "undirected" edges have a "from" and a "to" -- determined by how the edge was created using the Connect command.
- SetAlpha: objectID
Sets the alpha (transparency) of the object. 0 is completely transparent, 1 is completely opaque
	- objectID: The ID of the object to modify. The object must exists, or an exception will be thrown
- SetEdgeHighlight: fromID, toID, highlightVal
Mark an edge as either highlighted or unhighlighted, based on the value of highlightVal. Edges that are highlighted will pulse red.
	- fromID: The ID of the "From" direction of the edge
	- toID: The ID of the "To" direction of the edge
	- higlightVal: 0 or false, turn of higlighting. 1 or true, turn on highlighting.

### Special Commands

- Step: <No parameters>
The step command allows you to keep everything from happening at once. The way that most animations will work is that you will create a group of objects, then do a step, then do some movements, then do a step, then do more movements, then do a step, and so on. All commands that appear between adjacent steps will happen simultaneously. Each step represents where the animation will pause when it is in single-step mode.
- SetLayer objectID, newLayer
Sets the layer of the object. All objects default to layer 0, and the "shown" layer always defaults to 0. You can change the layers of different objects, and then change the list of which layers are currently shown, to show or hide objects dynamically. (This is often useful for allowing the user to show or hide information, or to alternate between different versions of a representation). An object will only appear if its layer is one of the layers listed to be shown. An edge will only appear of each of the objects that it connect are to be shown. While commands cannot be executed while an animation is running, the global set of visible layers can be changed while an animation is running
	- objectID: The ID of the object to modify. The object must exists, or an exception will be thrown
	- layer: The new layer for this object. Each object must live in one and only one layer (though any combination of layers can be shown at any given time)

## Simple Stack Example
이제는 정상적으로 수행되는 예제를 볼 것입니다. 간단한 스택(stack) 시각화 예제가 있습니다.
- AlgorithmLibrary/SimpleStack.js
- [실행 결과](https://www.cs.usfca.edu/~galles/visualization/SimpleStack.html)

아래는 초기화 작업을 수행하는 코드중 일부입니다. 시각화 자바스크립트의 시작은 아래의 코드와 같고 이는 언제든지 원하는 기능의 함수로 대체할 수 있습니다.

```js
function SimpleStack(am, w, h)
{
	this.init(am, w, h);
}

SimpleStack.prototype = new Algorithm();
SimpleStack.prototype.constructor = SimpleStack;
SimpleStack.superclass = Algorithm.prototype;
```

다음은 SimpleStack에서 사용하는 상수의 모습입니다. 심볼의 충돌을 피하기 위해 함수의 네임스페이스를 사용하였습니다.

```js
SimpleStack.ELEMENT_WIDTH = 30;
SimpleStack.ELEMENT_HEIGHT = 30;
SimpleStack.INSERT_X = 30;
SimpleStack.INSERT_Y = 30;
SimpleStack.STARTING_X = 30;
SimpleStack.STARTING_Y = 100;
SimpleStack.FOREGROUND_COLOR = "#000055"
SimpleStack.BACKGROUND_COLOR = "#AAAAFF"
```

다음은 생성자입니다. 기술적으로, 생성자는 아래와 같이 맨 처음에 위치합니다.

```
function SimpleStack( ...
```

그러나, 해당 생성자는 초기화 작업만을 수행합니다. -- 이 방법은 서브 클래스의 생성자들은 효율적으로 그들의 슈퍼 클래스의 생성자들을 호출할 수 있습니다. 이 경우의 초기화 함수를 위해서 간단한 작업이 필요합니다. 이 예제에서는 로드(load) 시간동안 캔버스(canvas)에 어떠한 요소도 추가하지 않습니다. 개발자가 해야할 일은 해당 내부 자료 구조를 설정하는 것뿐입니다. 시스템은 두 배열(실제 스택을 저장하는 배열(stackValues), 스택 요소의 오브젝트 ID를 저장하는 배열(stackID))의 추적을 유지합니다.

```js
SimpleStack.prototype.init = function(am, w, h)
{
	// Call the unit function of our "superclass", which adds a couple of
	// listeners, and sets up the undo stack
	SimpleStack.superclass.init.call(this, am, w, h);

	this.addControls();

	// Useful for memory management
	this.nextIndex = 0;

	this.stackID = [];
	this.stackValues = [];

	this.stackTop = 0;
}
```

다음은 알고리즘 컨트롤과 콜백 함수를 추가하는 매서드입니다. 아래는 어쩔 수 없이 복잡하게 구현한 부분입니다.

```
this.popButton.onclick = this.popCallback
```

이는 콜백 함수를 추가하는 부분입니다. 이것은 함수를 정상적으로 전달하지만, 정상적인 문맥은 전달하지 못합니다. -- 기본적으로 이것은 "this" 포인터를 저장하지 않고 함수의 포인터를 넘겨주고 있습니다. 그래서 "this" 포인터를 저장하기 전에 해당 함수에 "this" 포인터를 바인드(bind) 해주어야 합니다.

```js
SimpleStack.prototype.addControls =  function()
{
	this.controls = [];


    this.pushField = addControlToAlgorithmBar("Text", "");
    this.pushField.onkeydown = this.returnSubmit(this.pushField,  
                                               this.pushCallback.bind(this), // callback to make when return is pressed
                                               4,                           // integer, max number of characters allowed in field
                                               false);                      // boolean, true of only digits can be entered.
	this.controls.push(this.pushField);

	this.pushButton = addControlToAlgorithmBar("Button", "Push");
	this.pushButton.onclick = this.pushCallback.bind(this);
	this.controls.push(this.pushButton);

	this.popButton = addControlToAlgorithmBar("Button", "Pop");
	this.popButton.onclick = this.popCallback.bind(this);
	this.controls.push(this.popButton);
}
```

참고로 바인딩 하는 함수는 CustomEvents.js 파일에 구현되어 있고, 아래와 같습니다.

```js
Function.prototype.bind = function() {
	var _function = this;

	var args = Array.prototype.slice.call(arguments);
	var scope = args.shift()
	return function() {
		for (var i = 0; i < arguments.length; i++)
		{
			args.push(arguments[i]);
		}
		return _function.apply(scope, args);
	}
}
```

다음은 리셋 함수입니다. 모든 시각화는 리셋 매서드를 구현해야 합니다. 이 리셋 매서드는 init 함수를 호출한 직후에 모든 변수를 원래 상태로 되돌려야 합니다. 이 에제에서는 오직 2개의 중요한 변수를 가지고 있습니다. 여기서 stackID 와 stackValues 배열을 다시 만들 수 있지만, 이 경우에는 그럴 필요성이 없습니다. 왜냐하면, 두 배열의 현재 값에 대해 신경 쓸 필요가 없기 때문입니다. -- 스택의 top 값이 0인 경우에는 이를 읽기전에는 어떤 값이라도 쓸 수 있습니다.

```js
SimpleStack.prototype.reset = function()
{
	// Reset the (very simple) memory manager.
	//  NOTE:  If we had added a number of objects to the scene *before* any user
	//         input, then we would want to set this to the appropriate value based
	//         on objects added to the scene before the first user input
	this.nextIndex = 0;

	// Reset our data structure.  (Simple in this case)
	this.stackTop = 0;
}
```

다음은 콜백입니다. 개발자는 콜백 함수에 대해 직접적으로 어떠한 작업도 할 필요가 없습니다. -- 대신에, implementAction 매서드를 사용합니다. 이 매서드는 bound 함수(bind 매서드를 사용하는)와 한 매개변수를 가지고 와서, 이 매개변수를 사용하는 함수를 호출합니다. implementAction 은 또한 현재까지 수행되었던 모든 action 의 리스트를 저장합니다. 이로 인해 쉽게 undo 작업을 수행할 수 있습니다.

```js
SimpleStack.prototype.pushCallback = function()
{
	var pushedValue = this.pushField.value;

	if (pushedValue != "")
	{
		this.pushField.value = "";
		this.implementAction(this.push.bind(this), pushedValue);
	}

}

SimpleStack.prototype.popCallback = function()
{
	this.implementAction(this.pop.bind(this), "");
}
```

마지막으로, 지금부터는 시각화를 위한 핵심 코드입니다. -- 이 예제 코드는 잘 동작합니다. 대부분 action 을 구현하고 있고, 웹상에 무엇을 하고 있는지 기록하기 위해 cmd 호출을 사용하고 있습니다.

```js
SimpleStack.prototype.push = function(pushedValue)
{
	this.commands = [];

	this.stackID[this.stackTop] = this.nextIndex++;

	this.cmd("CreateRectangle", this.stackID[this.stackTop],
			                    pushedValue,
								SimpleStack.ELEMENT_WIDTH,
								SimpleStack.ELEMENT_HEIGHT,
								SimpleStack.INSERT_X,
			                    SimpleStack.INSERT_Y);
	this.cmd("SetForegroundColor", this.stackID[this.stackTop], SimpleStack.FOREGROUND_COLOR);
	this.cmd("SetBackgroundColor", this.stackID[this.stackTop], SimpleStack.BACKGROUND_COLOR);
	this.cmd("Step");
	var nextXPos = SimpleStack.STARTING_X + this.stackTop * SimpleStack.ELEMENT_WIDTH;
	var nextYPos = SimpleStack.STARTING_Y;
	this.cmd("Move", this.stackID[this.stackTop], nextXPos, nextYPos);
	this.cmd("Step"); // Not necessary, but not harmful either
	this.stackTop++;
	return this.commands;
}

SimpleStack.prototype.pop = function(unused)
{
	this.commands = [];

	if (this.stackTop > 0)
	{
		this.stackTop--;

		this.cmd("Move", this.stackID[this.stackTop], SimpleStack.INSERT_X, SimpleStack.INSERT_Y);
		this.cmd("Step");
		this.cmd("Delete", this.stackID[this.stackTop]);
		this.cmd("Step");

		// OPTIONAL:  We can do a little better with memory leaks in our own memory manager by
		//            reclaiming this memory.  It is recommened that you *NOT* do this unless
		//            you really know what you are doing (memory management leads to tricky bugs!)
		//            *and* you really need to (very long runnning visualizaitons, not common)
		//            Because this is a stack, we can reclaim memory easily.  Most of the time, this
		//            is not the case, and can be dangerous.
		// nextIndex = this.stackID[this.stackTop];
	}
	return this.commands;
}
```

현재까지 대부분이 완성되었습니다. 아래는 애니메이션이 작동하는 동안 알고리즘 컨트롤을 enable/disable 하는 코드입니다.

```js
// Called by our superclass when we get an animation started event -- need to wait for the
// event to finish before we start doing anything
SimpleStack.prototype.disableUI = function(event)
{
	for (var i = 0; i < this.controls.length; i++)
	{
		this.controls[i].disabled = true;
	}
}

// Called by our superclass when we get an animation completed event -- we can
/// now interact again.
SimpleStack.prototype.enableUI = function(event)
{
	for (var i = 0; i < this.controls.length; i++)
	{
		this.controls[i].disabled = false;
	}
}
```

알고리즘 시각화를 구현하기 위해 SimpleStack 코드에서 필요한 부분을 만들거나 수정하고, 불필요한 부분을 삭제할 수 있습니다.

```js
////////////////////////////////////////////////////////////
// Script to start up your function, called from the webapge:
////////////////////////////////////////////////////////////

var currentAlg;

function init()
{
	var animManag = initCanvas();
	currentAlg = new SimpleStack(animManag, canvas.width, canvas.height);

}
```


## HTML Template
이 시각화 시스템은 HTML과 자바스크립트의 조합입니다. 이를 위해 자바스크립트를 삽입할 웹 페이지가 필요하고, 웹 페이지는 아래와 같은 요소들이 필요합니다.

- 모든 필요한 스크립트 파일들을 로드하기 위해 헤더에 ```<script>``` 테그를 추가한다. 이 파일들은 의존도(dependency)에 따라 정확한 순서대로 include 되어야 한다.(자바스크립트가 HTML에 이 모든 파일을 수동으로 삽입하는 것을 방지하는 ```#include``` 와 같은 표준 메커니즘을 가지고 있다면 좋을 것입니다. 하지만 그 주변에 자바스크립트 호출을 사용하여 ```<script>``` 태그를 동적으로 삽입하는 몇 가지 방법이 있지만 모든 브라우저가 이러한 다소 복잡한 방법으로 동작하지는 않습니다. 순서가 정확하다면, 모든 파일의 탐색은 어디서든 작동할 것입니다.)
- 알고리즘별 컨트롤을 위치시킬 "algoControlSection" id가 포함된 빈 테이블
- 애니메이션을 표현할 ```canvas``` element
- 일반적인 애니메이션 컨트롤을 위치시킬 "GeneralAnimationControls" id가 포함된 빈 테이블
- 시각화를 시작하는 ```<body>``` 테그에서의 속성 ```onload = "init()"```

가장 쉬운 방법은 template.html 파일의 템플릿을 사용하여, 필요에 맞게 값을 바꾸는 것입니다.

아래는 template.html 의 코드이고, ```Place ...``` 이 부분을 필요에 맞게 변경할 수 있습니다.

```html
<!DOCTYPE html>
<html>
    <head>

        <title>
           Place your title here
        </title>

        <!-- css sheet for how the page is laid out -->

        <link rel="stylesheet" href="visualizationPageStyle.css">


        <!-- jqueury stuff.  Only used for the animation speed slider. -->
        <link rel="stylesheet" href="ThirdParty/jquery-ui-1.8.11.custom.css">

        <script src="ThirdParty/jquery-1.5.2.min.js"></script>
        <script src="ThirdParty/jquery-ui-1.8.11.custom.min.js"></script>

        <!-- Javascript for the actual visualization code -->
        <script type = "text/javascript" src = "AnimationLibrary/CustomEvents.js"> </script>
        <script type = "text/javascript" src = "AnimationLibrary/UndoFunctions.js"> </script>
        <script type = "text/javascript" src = "AnimationLibrary/AnimatedObject.js"> </script>
        <script type = "text/javascript" src = "AnimationLibrary/AnimatedLabel.js"> </script>
        <script type = "text/javascript" src = "AnimationLibrary/AnimatedCircle.js"> </script>
        <script type = "text/javascript" src = "AnimationLibrary/AnimatedRectangle.js"> </script>
        <script type = "text/javascript" src = "AnimationLibrary/AnimatedLinkedList.js"> </script>
        <script type = "text/javascript" src = "AnimationLibrary/HighlightCircle.js"> </script>
        <script type = "text/javascript" src = "AnimationLibrary/Line.js"> </script>
        <script type = "text/javascript" src = "AnimationLibrary/ObjectManager.js"> </script>
        <script type = "text/javascript" src = "AnimationLibrary/AnimationMain.js"> </script>
        <script type = "text/javascript" src = "AlgorithmLibrary/Algorithm.js"> </script>
        <script type = "text/javascript" src = "Place path to your javascript file here"> </script>


     </head>

    <body onload="init();" class="VisualizationMainPage">

        <div id = "container">

            <div id="header">  
                <h1>Place your Header here </h1>
            </div>

            <div = id = "mainContent">

                <div id = "algoControlSection">
                    <!-- Table for buttons to control specific animation (insert/find/etc) -->
                    <!-- (filled in by javascript code specific to the animtion) -->
                    <table id="AlgorithmSpecificControls"> </table>
                </div>

                    <!-- Drawing canvas where all animation is done.  Note:  can be resized in code -->

                <canvas id="canvas" width="1000" height="500"></canvas>

                <div id = "generalAnimationControlSection">
                    <!-- Table for buttons to control general animation (play/pause/undo/etc) ->
                    <!-- (filled in by javascript code, specifically AnimationMain.js)  -->

                    <table id="GeneralAnimationControls">  </table>     
                </div>

            </div> <!-- mainContent -->

            <div id="footer">  
                <p><a href="Algorithms.html">Algorithm Visualizations</a></p>
            </div>

        </div><!-- container -->
    </body>
</html>
```


## Quicks and Advanced Techniques

### Object Display Order
만약 2개의 오브젝트가 겹친다면, 아래의 규칙에 따라 어느 오브젝트가 위에 그려질지 순서가 정해집니다.

- non-highlighted 모든 아이템들은 highlighted 아이템들 전에 그려진다.(그래서 highlighted 아이템은 non-highlighted 아이템 위에 나타납니다.)
- 같은 highlight 상태의 모든 아이템들은 식별자 번호(id) 순서대로 그려진다.(id 번호가 큰 오브젝트가 id 번호가 작은 오브젝트 앞에 그려질 것입니다.)

이 시스템은 그리 정교하지는 않지만, 충분히 잘 동작합니다. 만약 오브젝트 A 가 오브젝트 B 앞에 나타나길 원한다면, 오브젝트 A의 id 번호가 B보다 커야합니다. 그리고 더 정교한 시스템을 원하다면, 미래에 수정된 버전이 나올 수도 있습니다.

### Debugging
자바스크립트를 개발하고 있다면 Firebug는 자바스크립트 디버거로서 매우 괜찮습니다.(무료) 그러나 이 프로그램은 breakpoint(중단점) 에 대한 문제가 있습니다. 이 시스템의 애니메이션은 자바스크립트 setTimeout 명령에 크게 의존합니다. 시간초과(timeout)후에 Firebug의 중단점에 도달하면 시간초과가 손실될 것입니다. 잘못된 코드 부분에 중단점을 설정한다면 애니메이션이 중단될 수 있습니다.
