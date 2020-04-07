---
layout: single
title: "WAI-ARIA 2편"
excerpt: "WAI-ARIA 실무 적용 예제를 자세히 알아보자!"
categories: [MARKUP]
tags: [wai-aria, accessibility, 웹접근성]
toc: true
toc_sticky: true
toc_label: "WAI-ARIA 2편"
---

# WAI-ARIA 실무 적용

## Tab UI
```html
<ul class="tab-list" role="tablist">
  <li id="tab1" role="tab" aria-controls="section1" aria-selected="true"  tabindex="0">HTML</li>
  <li id="tab2" role="tab" aria-controls="section2" aria-selected="false" tabindex="0">CSS</li>
  <li id="tab3" role="tab" aria-controls="section3" aria-selected="false" tabindex="0">Javascrip</li>
</ul>
<div class="tab-contents">
  <section id="section1" role="tabpanel" aria-labelledby="tab1">
    <p>section1</p>
    <a href="#">상세 보기</a>
  </section>
  <section id="section2" class="hidden" role="tabpanel" aria-labelledby="tab2">
    <p>section2</p>
    <a href="#">상세 보기</a>
  </section>
  <section id="section3" class="hidden" role="tabpanel" aria-labelledby="tab3">
    <p>section3</p>
    <a href="#">상세 보기</a>
  </section>
</div>
```
```javascript
const tabs = document.querySelectorAll('[role=tab]');
const tabPanels = document.querySelectorAll('[role=tabpanel]');
let currentTab = tabs[0];
const firstEle = tabs[0];
const lastEle = tabs[tabs.length -1];

function tabSelect (target){
  const beforePanel = document.querySelector(`#${currentTab.getAttribute("aria-controls")}`);
  const selectedPanel = document.querySelector(`#${target.getAttribute("aria-controls")}`);

  currentTab.setAttribute("aria-selected",false);
  target.setAttribute("aria-selected",true);
  beforePanel.classList.add('unvisual')
  selectedPanel.classList.remove('unvisual');
  currentTab = target;
}

tabs.forEach(t=>{
  t.addEventListener('click', ()=>{
    tabSelect(t);
  });

  t.addEventListener("keyup",e=>{
    const keycode = e.keyCode;
    const nextEle = t.nextElementSibling;
    const prevEle = t.previousElementSibling;

    
    if(keycode === 39 || keycode === 40){
      if(lastEle.id === t.id){
        firstEle.focus();
        tabSelect(firstEle);
      }else{
        nextEle.focus();
        tabSelect(nextEle);
      } 
    }
    else if(keycode === 37 || keycode === 38){
      if(firstEle.id === t.id){
        lastEle.focus();
        tabSelect(lastEle);
      }else{
        prevEle.focus();
        tabSelect(prevEle);
      } 
    }
    else if(keycode === 36){ //home
      firstEle.focus();
      tabSelect(firstEle);
    }
    else if(keycode === 35){ //end
      lastEle.focus();
      tabSelect(lastEle);
    }
  });

  t.addEventListener("keydown",e=>{
    const section = document.querySelector(`#${t.getAttribute("aria-controls")}`);
    const linkEles = section.querySelectorAll("a");

    e.preventDefault();

    if(e.keyCode === 9){     
      linkEles[0].focus();
    }
  });
});

tabPanels.forEach(p=>{
  p.querySelectorAll("a")[0].addEventListener("keydown",e=>{
    const tab = document.querySelector(`#${p.getAttribute("aria-labelledby")}`);
    if(e.keyCode === 9 && e.shiftKey){
      e.preventDefault();
      tab.focus();
    }
  });
});
```
## 자동완성

## ID/PASSWORD & 에러메시지
입력 양식의 오류가 발생했을 때, 스크린리더 사용자에게 알리는 방식은 두 가지가 있다.   
**첫번째** 로는 아래와 같이 기본적으로 작성된 코드에서 에러가 발생할 경우, 에러 표시 영역의 id값을 에러가 발생한 `<input>`의 `aria-describedby` 값으로 변경해주고, `aria-invalid="true"`를 추가, focus를 전달해주면 된다. 이후, 유효한 값을 입력받으면 `aria-describedby` error id 값과 `aria-invalid="true"`를 제거한다.
- `aria-required="true"` : 필수 입력 항목 표시
- `aria-describedby` : 부연 설명 영역의 id와 연결
- `aria-invalid` : 입력 양식의 입력값이 유효하지 않음을 표시

```html
<form onsubmit="confirmForm();return false;">
  <legend>로그인</legend>
  <div class="loginBox">
    <div class="box">
      <label for="user_id" aria-required="true">아이디</label>
      <input type="text" id="user_id" aria-describedby="id_desc" />
      <div class="info" id="id_desc">5~20자의 영문 소문자, 숫자와 특수기호(_), (-)만 사용 가능합니다.</div>
      <div class="error" id="id_error">유효하지 않은 아이디입니다.</div>
    </div>
    <div class="box">
      <label for="user_passwd" aria-required="true">비밀번호</label>
      <input type="text" id="user_passwd" aria-describedby="passwd_desc" />
      <div class="info" id="passwd_desc">6~16자 영문 대 소문자, 숫자, 특수문자를 사용하여 입력해 주세요.</div>
      <div class="error" id="pass_error">유효하지 않은 비밀번호입니다.</div>
    </div>
  </div>
  <button id="frmBtn">확인</button>
</form>
```
```javascript
const userId = document.querySelector("#user_id");
const idError = document.querySelector("#id_error");

const confirmForm = () =>{
  idError.style.display="block";
  userId.setAttribute("aria-describedby", "id_error");
  userId.setAttribute("aria-invalid", true);
  userId.focus();
}
```
**두번째** 로는 `aria-live="assertive"`로 실시간으로 변경된 컨텐츠를 스크린리더 사용자에게 알려주는 방식이다. 이 방식은 특정 폼과 연결할 수 없는 유형일 경우 사용한다.   
내용 없이 비어있는 요소에 `aria-live="assertive"`과 `role="alert"`을 작성해준다. 양식 작성 중, 에러가 발생할 때 에러표시 영역에  내용을 채워주게 되면 스크린리더 사용자에게 즉각 변경 내용을 전달한다.
```html
<form>
  <legend>로그인</legend>
  <div class="loginBox">
    <div class="box">
      <label for="user_id" aria-required="true">아이디</label>
      <input type="text" id="user_id" aria-describedby="id_desc" />
      <div class="info" id="id_desc">5~20자의 영문 소문자, 숫자와 특수기호(_), (-)만 사용 가능합니다.</div>
    </div>
    <div class="box">
      <label for="user_passwd" aria-required="true">비밀번호</label>
      <input type="text" id="user_passwd" aria-describedby="passwd_desc" />
      <div class="info" id="passwd_desc">6~16자 영문 대 소문자, 숫자, 특수문자를 사용하여 입력해 주세요.</div>
    </div>
    <div class="error" id="error" role="alert" aria-live="assertive"></div>
  </div>
  <button id="frmBtn">확인</button>
</form>
```
```javascript
const userId = document.querySelector("#user_id");
const error = document.querySelector("#error");

userId.addEventListener("keydown",function(){
  error.textContent = "유효하지 않은 아이디입니다.";
});
```
## 다중폼 서식
서식 중 한 개의 레이블에 여러 폼을 작성해야 하는 경우, 이것을 감싸는 영역에 `role="group"`와 `aria-labelledby="레이블 id 지정"`을 작성한다. 그리고 입력 폼에 `aria-label`로 `<label>` 요소의 역할을 대체한다.
```html
<div class="field-item" role="group" aria-labelledby="label-tel">
  <span class="label" id="label-tel">전화번호</span>
  <input type="text" name="usr-tel-1" id="usr-tel-1" aria-label="국번" /> -
  <input type="text" name="usr-tel-2" id="usr-tel-2" aria-label="가운데 자리" /> -
  <input type="text" name="usr-tel-3" id="usr-tel-3" aria-label="마지막 자리" />
</div>
```
## 버튼
버튼 요소에는 `<button>`요소를 사용하고, `<div>`, `<span>`요소로 구현할 경우 `tabindex`와 `space bar` && `enter` 키로 버튼이 실행될 수 있게 처리되어야 한다.   
토글 버튼을 구현할 경우, 눌림과 눌림 해제를 표시하는 `aria-pressed="true or false"`를 지정 해주어야 한다.   
또, 사용할 수 없는 비활성화 버튼의 경우에는 `aria-disabled="true or false"`를 지정한다.

## 라디오 버튼
`<input type="radio" />`로 구현되는 경우가 아닌 커스텀 컴포넌트로 구현할 경우에 필요한 기술이다. 되도록 `<ul>`, `<li>`로 구현하고, `<ul role="radiogroup" aria-labelledby="레이블 id 지정">`또는 `<ul role="group" aria-labelledby="레이블 id 지정">`로 role를 부여하고 제목 요소와 연결한다. `<li role="radio" aria-checked="true or flase" tabindex="0">`는 role과 선택된 요소인지를 `aria-checked`로 알려준다.   
인터렉션은 `→` 방향키, `↓` 방향키 사용으로 오른쪽으로 이동과 선택되어야 하고 `←`방향키, `↑`방향키로 왼쪽으로 이동과 선택되어야 한다. 선택된 항목이 없을 경우, `space bar` 키로 선택할 수 있어야 한다.
선택된 항목이 없을 경우 첫번째 항목과, 선택된 항목으로 탭키가 이동되도록 구현해야 한다.

```html
<h1 id="job">직업</h1>
<ul role="radiogroup" aria-labelledby="job" id="jobGroup">
  <li role="radio" aria-checked="false" tabindex="0">사무직</li>
  <li role="radio" aria-checked="false" tabindex="-1">전문직</li>
  <li role="radio" aria-checked="false" tabindex="-1">서비스직</li>
</ul>
<input type="radio" name="job" id="job1"><label>사무식</label>
<input type="radio" name="job" id="job2"><label>전문직</label>
<input type="radio" name="job" id="job3"><label>서비스직</label>
```
```javascript
const jobs = document.querySelectorAll("#jobGroup > li[role='radio']");
const firstJob = jobs[0];
const lastJob = jobs[jobs.length - 1];

function checkedRadio (current, target){
  if(current){
    current.setAttribute("tabindex","-1");
    current.setAttribute("aria-checked",false);
  }
  target.setAttribute("tabindex","0");
  target.setAttribute("aria-checked",true);
  target.focus();
}

jobs.forEach(j=>{
  j.addEventListener("keydown",e=>{
    const keycode = e.keyCode;
    const nextEle = j.nextElementSibling;
    const prevEle = j.previousElementSibling;

    if(e.altKey || e.ctrlKey){
      return true;
    }

    if(keycode === 39 || keycode === 40){        
      if(lastJob === j){
        e.preventDefault();
        checkedRadio(j,firstJob);
      }else {
        checkedRadio(j,nextEle);
      }          
    }

    if(keycode === 37 || keycode === 38){
      if(firstJob === j){
        e.preventDefault();
        checkedRadio(j,lastJob);
      }else {
        checkedRadio(j,prevEle);
      }         
    }
    
    if(keycode === 32 && j.getAttribute("aria-checked") === "false"){
      checkedRadio(null, j)
    }
  });
});
```

## 체크박스
`<input type="checkbox" />`사용을 할 수 없어 다른 요소를 이용하여 커스텀 컴포넌트를 생성해야 하는 경우, `<ul>`, `<li>`요소로 작성하는 것을 권장한다. `<ul role="group" aria-labelledby="fruits">`와 같이 `role`과 `aria-labelledby` 속성을 작성하고, `<li role="checkbox" aria-checked="false" tabindex="0">` 요소에는 `role`과 선택 여부를 표시하는 `aria-checked`를 작성 해야한다.    인터렉션으로 `space bar` 키로 항목을 선택 또는 해제가 가능해야 한다. 탭키로 항목을 이동할 수 있도록 해야한다.
```html
<h1 id="fruits">좋아하는 과일</h1>
<ul role="group" aria-labelledby="fruits" id="fruitsGroup">
  <li role="checkbox" aria-checked="false" tabindex="0">사과</li>
  <li role="checkbox" aria-checked="false" tabindex="0">바나나</li>
  <li role="checkbox" aria-checked="false" tabindex="0">포도</li>
</ul>
```
```javascript
const fruits = document.querySelectorAll("#fruitsGroup > [role='checkbox']");
fruits.forEach(f=>{
  f.addEventListener("keyup",e=>{
    if(e.keyCode === 32) {
      f.setAttribute("aria-checked", !(f.getAttribute("aria-checked") === 'true'));
    }
  });
});
```

## 레이어팝업
- 팝업 호출 버튼 클릭 시, 팝업창에 포커스 이동
- 팝업이 닫힐 경우 `aria-hidden="true"`, 열릴 경우 `aria-hidden="false"`
- 탭키 사용시 팝업 안에서 순환적 이동
- ESC키/닫기버튼 사용시 팝업종료
- 팝업 종료시 팝업 호출 버튼으로 포커스 이동
- 팝업 첫번째 요소에서 `tab` && `shift` 키 사용시, 팝업 종료시와 동일한 결과 표현

```html
<button id="triggerBtn" tabindex="0" data-layer="layerPopup">팝업열기</button>
<a href="#">본문 링크1</a>
<a href="#">본문 링크2</a>
<div id="layerPopup" class="layer_popup" aria-labelledby="layerTitle" aria-hidden="true">
  <h1 id="layerTitle" tabindex="0">레이어팝업</h1>
  <a href="#">레이어 링크1</a>
  <ul>
    <li>항목1</li>
    <li>항목2</li>
    <li>항목3</li>
  </ul>
  <a href="#">레이어 링크2</a>
  <button class="closeBtn">팝업닫기</button>
</div>
<a href="#">본문 링크3</a>
<a href="#">본문 링크4</a>
```
```javascript
//레이어팝업 열기
function layperPopup(trigger){
  const target = document.querySelector(`#${trigger.getAttribute("data-layer")}`);
  const title = target.querySelector("h1");
  const focusEls = target.querySelectorAll("a, button");
  const prevBtn = focusEls[focusEls.length-2];
  const closeBtn = focusEls[focusEls.length-1]; //항상 닫기 버튼은 레이어팝업 마지막 요소로 배치

  target.setAttribute("aria-hidden","false");
  target.style.display="block";
  target.addEventListener("keydown", function(e){
    if(e.keyCode === 27) {
      closePopup(trigger,target);
    }
  });
  trigger.setAttribute("tabindex","-1");
  closeBtn.addEventListener("click",()=>closePopup(trigger,target));
  closeBtn.addEventListener("keydown",function(e){
    if(e.keyCode === 9){ //레이어닫기 버튼에서 tab키 이용시 타이틀로 포커스 이동
      e.preventDefault();
      title.focus();
    }
    if(e.keyCode === 9 && e.shiftKey){
      prevBtn.focus();
    }
  });
  title.focus();
  title.addEventListener("keydown",function(e){
    if(e.keyCode === 9 && e.shiftKey){ //타이틀에서 tab & shift 카 이용시 레이어팝업을 호출한 요소로 포커스 이동.
      e.preventDefault();
      closePopup(trigger,target);
    }
  });
}

//레이어팝업 닫기
function closePopup(trigger, target){
  trigger.setAttribute("tabindex","0");
  trigger.focus();
  target.setAttribute("aria-hidden","true");
  target.style.display="none";
}

const triggerBtn = document.querySelector("#triggerBtn");
triggerBtn.addEventListener("click",()=>layperPopup(triggerBtn));
```
## 툴팁
```css
[role="tooltip"]{display:none; width:200px;}
button:hover + [role="tooltip"],
button:focus + [role="tooltip"] {display:block;}
```
```html
<button aria-describedby="tooltipBox">툴팁이란?</button>
<div id="tooltipBox" role="tooltip">
  툴팁이란, 말풍선 또는 툴팁은 공통 그래픽 사용자 인터페이스 요소이다. 
  마우스 포인터라 불리는 커서와 함께 동작한다. 
  사용자가 커서에 항목을 클릭하지 않고 가리키면 조그마한 상자가 항목 위에 나타나서 보충 설명을 보여 준다.
</div>
```
## 필수 입력 항목
서식 작성 폼의 경우 필수 입력 항목을 `*`로 표시할 경우 숨김 텍스트를 사용하여 제공하는데, HTML5의 경우 `required` 속성으로 대체할 수 있다. 하지만 유저 에이전트에 따라 제대로 `required`를 지원하지 못하거나 HTML5로 작성된 문서가 아닐 경우는 제대로 스크린 리더 사용자에게 정보를 제공하지 못한다.   
`aria-required` 를 사용하면 이런 문제점들을 해소할 수 있다.
```html
<label for="user_id">아이디</label>
<input type="text" id="user_id" aria-required="true" />
```