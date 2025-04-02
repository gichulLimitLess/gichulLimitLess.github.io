---
# [해당 부분은 인트로(글 제목, 카테고리, 썸네일 이미지 등) 관련 정보]
title: "Prop-types.. TypeScript..? 니넨 누구임? (React에서 props 타입 관리하는 법)"
categories: [React]
tags: [React, props]
image:
  path: "../assets/img/posting-images/20250402/0402-thumbnail.jpeg"
  alt: "어느 곳에서든 안전에 유의합시다. Type Error에도 항상 신중해야 합니다. 이게 뭔 연관이냐고 묻는다면.. 반박시 여러분 말이 다 맞습니다."
  width: 1200   # 이미지의 너비 조정
  height: 1200   # 이미지의 높이 조정
  # dark: "/assets/img/dark-cover.jpg"  # 다크 모드에서 다른 이미지 사용
---

오늘 수업 시간에 React에서 컴포넌트에 넘어오는 props의 타입을 지정하는 방법으로, `prop-types`라는 친구를 새로 배웠다. 난 그동안.. React를 맨땅에 헤딩 하면서.. 프로젝트부터 하며 배우다 보니, TypeScript만 주구장창 써왔는데, 새로운 친구를 보니.. 이 친구는 TypeScript랑 뭐가 다른 건지.. 갑자기 궁금해졌다.

뭐 대충 찾아보니.. prop-types라는 건 옛날부터 써온 오래된 친구이고, TypeScript는 요즘 따라 많이 쓰는 기법이라고는 하던데, 하여튼 둘이 누구인지 간단히 알아보자!

<br>

## 🧑‍🔧`Prop-types` vs TypeScript

### `Prop-types`

우선 `prop-types` 라는 친구를 사용한 예시 코드부터 한 번 살펴보자. 아래 코드는 클래스 컴포넌트(Component class를 상속 받아서 사용중이잖아~)를 사용 중이다. 자세한 설명은 코드의 주석으로 남겨 놓았으니 확인 부탁한다! ~~(반박 시 여러분 말씀이 다 맞습니다(?))~~

```jsx
import React, { Component } from "react";
import PropTypes from "prop-types";

class Welcome extends Component {
  render() {
    const { name, style, children } = this.props;
    return (
      <div>
        <h1 style={style}>Hello, {name}</h1>
        <h2>content: {children}</h2>
      </div>
    );
  }
}

//props가 안 들어올 경우 대비해서, defaultProps로 기본값도 넣어놓음
Welcome.defaultProps = {
  name: "react",
  style: {
    backgroundColor: "gray",
    color: "aqua",
    fontSize: "30px",
    fontWeight: "bold",
    padding: 6,
  },
};

/*
	prop-types를 이용해서 props의 타입을 정의하고 있는 중!
	--> name은 string이고 필수(isRequired)
	--> style은 object이고 선택
	--> children도 들어올 수 있음
	(children prop은 React에서 일반적으로 사용되는 방법, 
		공통 컴포넌트를 분리하고 재사용하기 위해 사용됨)
*/
Welcome.propTypes = {
  name: PropTypes.string.isRequired,
  style: PropTypes.object,
  children: PropTypes.node,
};

export default Welcome;
```

### TypeScript

요즘 프로젝트들은 대부분 함수형 컴포넌트를 쓰고(아직 상당히 많은 회사들이 클래스형 컴포넌트를 쓰고 있다고는 하더라), TypeScript를 같이 쓰는 경우가 많다고 한다. 위에 있던 Welcome 어쩌구 예제 코드를 TypeScript 스타일로 바꿔보면 아래와 같다.

```jsx
import React, { ReactNode, CSSProperties } from "react";

interface WelcomeProps {
  name?: string;
  style?: CSSProperties;
  children?: ReactNode;
}

const Welcome: React.FC<WelcomeProps> = ({
  name = "react",
  style = {
    backgroundColor: "gray",
    color: "aqua",
    fontSize: "30px",
    fontWeight: "bold",
    padding: 6,
  },
  children,
}) => {
  return (
    <div>
      <h1 style={style}>Hello, {name}</h1>
      <h2>content: {children}</h2>
    </div>
  );
};

export default Welcome;
```

방금 예제에서는 `prop-types`가 아닌 `interface` 를 사용해서 props의 타입을 정리했고, defaultProps 어쩌구 하면서 기본값 설정 했던 거 대신에, props의 default 값을 함수 파라미터에서 바로 설정해 버린 것을 확인할 수 있다. ~~(이게 훨씬 직관적이지 않니…? 아님 말고..)~~

<br>

## 𝝏 아니 그래서 둘의 차이점이 뭐임?

간단하게 표로 정리해준다.

| 항목 | prop-types | TypeScript |
| --- | --- | --- |
| 타입 검사 시점 | 실행할 때 (런타임) | 코드 짤 때 (컴파일 타임) |
| 기본값 설정 방식 | `defaultProps` | 함수 매개변수에서 직접 |
| 오류 발생 시 | 콘솔 경고 | 컴파일 에러 |
| 사용 범위 | React 전용 | JavaScript/React 어디든 |
| 장점 | 가볍게 추가 가능, 빠르게 쓸 수 있음 | 타입 강제력 👍, 자동완성/에디터 지원 좋음 |
| 단점 | 복잡한 타입 다루기 힘듦, 성능 오버헤드 | 설정 복잡, TS 잘 몰랐을 땐 진입장벽 있음 |

느끼는 점 있을까…? `prop-types` 를 기존에 써서 어쩔 수 없이 유지/보수를 위해 사용하는 거 아니면.. 그냥 마음 편하게 TypeScript를 써보도록 하자!고 강력하게 주장하는 바이다(??)

참고로, React 19 버전에서부터는 props-types와 defaultProps가 더 이상 공식 지원되지 않는다. (뭐, 지금 돌려보면 돌아가긴 하는데, 점점 deprecated되는 중이니까 참고하자) 그러니까, 지금 시점에서 새로 프로젝트를 시작해야 한다면, TypeScript로 작성하는 걸 훨씬 추천한다!

[관련 내용 문서 보기](https://mycodings.fly.dev/blog/2024-07-14-react-19-tutorial-5-breaking-changes-prop-types-context-ref?)

<br>

## 🧹마무리

맨땅에 헤딩 하면서 무의식적으로 사용했던 TypeScript, 그동안 난 “왜” 쓰는지를 몰랐었고, 그리고 굳이 찾아보려고 하지도 않았던 것 같다. 그런데, 이번 수업 시간에 생겼던 호기심 덕분에 이걸 우리가 왜 쓰고 있는 건지 깊은 탐구를 할 수 있던 좋은 시간이었다.

- 타입을 미리 정의하면 실수를 줄일 수 있고
- 코드를 짤 때부터 type error를 방지하기 위해 지원을 받고
- 에러도 빠르게 잡을 수 있고

이 역할을 옛날엔 `prop-types`라는 React 전용 기능을 사용했지만, 이제는 TypeScript라는 조금 더 범용적인 방법을 사용하게 된 것이라 이해하고 갔으면 좋겠다🥹