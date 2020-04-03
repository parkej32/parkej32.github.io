---
layout: single
title: "WAI-ARIA 1편"
excerpt: "W3C에서 접근성과 상호 운용성을 개선하기 위해 발표한 기술 명세가 WAI-ARIA (Web Accessibility
Initiative - Web Accessible Rich Internet Applications) 이다."
categories: [MARKUP]
tags: [wai-aria, accessibility, 웹접근성]
toc: true
toc_sticky: true
toc_label: "WAI-ARIA 1편"
---

# WAI-ARIA 란?
역동적이고 편리함을 제공하는 RIA의 등장으로 웹기술이 발달하게 되었지만, 모든 사람들에게 동등한 서비스를 제공하지 못하였다. 스크린리더 사용자는 Ajax로 업데이트된 내용을 인지하지 못하거나 포커스를 잃어버리는 등의 정확한 컨텐츠를 인지하지 못하는 일이 발생하게 되었다. 그래서 W3C에서 접근성과 상호 운용성을 개선하기 위해 발표한 기술 명세가 WAI-ARIA (Web Accessibility
Initiative - Web Accessible Rich Internet Applications) 이다.

## WAI-ARIA의 목적
WAI-ARIA는 마크업의 역할 (Role), 속성(Property), 상태(State) 정보를 추가할 수 있도록 지원한다. 이렇게 추가된 정보는 웹 브라
우저에 의해 자동으로 해석되어 각각의 운영체제(OS)의 접근성 API로 변환되도록 설계되었다. 이것은 사용자가 웹 애플리케이션을 사용할 때, 데스크탑 애플리케이션의 동작과 유사하게 인식하고 작동하기 때문에 보다 향상된 UX를 제공하게 된다.

## WAI-ARIA의 사용 주의사항
- 중복 사용 : HTML5로 버전업이 되면서 시멘틱 태그들이 다수 추가되었다. WAI-ARIA를 사용할 경우, HTML5의 시멘틱 태그와 WAI-ARIA Role과 겹치지 않도록 사용하여야 함.
- 시멘틱 태그 우선 사용 : HTML 태그로 제공할 수 있는 방법을 우선으로 하고 차선책으로 WAI-ARIA를 사용하여야 한다는 것을 명심해야 한다.
- 키보드 보장 : `<span role="button"></span>` 으로 사용하였다면, `enter`키와 `space bar`키로 동작할 수 있도록 제공되어야 함.
- 숨김 컨텐츠 : 단순히 화면에서만 보이지 않게 하기 위한 컨텐츠에 `aria-hidden="true"`를 사용하면 안됨.
- 가시적 컨텐츠 : 특정한 의미가 없고 가시적으로 보여주기 위한 컨텐츠의 경우에만 `role="presentation"` 사용.

## WAI-ARIA Role과 HTML5 태그 알아보기
![landmark](/assets/images/accessibility/landmark.png){:width="100%"}
- `role="banner"` : 비슷한 의미로 `<header>`가 있으며, `<header role="banner"></header>`로 사용될 수 있으며, 이 경우 한 페이지에 한 개의 `<header>`만 사용 권장함.
- `role="navigation"` : 동일한 역할의 요소는 `<nav>`이 있음. 중복 사용할 수 없고, 다른 페이지로의 이동을 위한 링크 영역을 나타내며, 메인 메뉴와 서브 메뉴 등에 사용됨.
- `role="main"` : 동일한 역할의 요소는 `<main>`이 있음. 중복 사용할 수 없고, 한 페이지에 한 개만 사용할 수 있으며 `<aside>`, `<article>`, `<footer>` 하위 요소로 사용될 수 없음.
- `role="complementary"` : 동일한 역할의 요소는 `<aside>`가 있음. 중복 사용할 수 없고, 현재 날씨, 관련 기사 또는 주식 정보 등의 부가 정보를 표현함.
- `role="form"` : 동일한 역할의 요소는 `<form>`이 있음. 서버로 전송될 컨텐츠를 포함할 때 사용.
- `role="application"` : 동일한 역할은 없음. `<div>`와 같은 그룹 역할을 하는 요소로 대체할 수 있음.
- `role="contentinfo"` : 비슷할 의미로 `<footer>`가 있으며, `<footer role="contentinfo"></footer>`로 사용되었다면 한 페이지의 한 개의 `<footer>`만 사용 권장함.
- `role="search"` : 동일한 역할은 없음. 검색의 역할을 담당하는 서식 영역임을 의미하며 `<div>` 또는 `<form>` 요소를 사용하는 것을 권장.