---
layout: post
title: "LifeCycleHook"
date: 2018-10-02 00:53:14 +0100
categories: study
author: dongwon
location: Seoul, Korea
description: LifeCycleHook
tail: I'm a front developer
---

---

# 라이프사이클

## 1. 컴포넌트 초기생성

일단 컴포넌트가 브라우저에 나타나기 전,후에 호출되는 API 들이 있다.

### contructor : 이 부분은 컴포넌트의 생성자 함수이다. 컴포넌트가 새로 만들어질 때마다 이 함수가 호출됨

### componentWillMount

이 API 는 컴포넌트가 화면에 나가기 직전에 호출되는 API 이다. 예전에는 브라우저가 아닌 환경에서도 호출되는 용도로 사용되었었는데 지금은 deprecated 중이다. componentDidMount 가 이 API 의 역할을 하는중이다.

### componentDidMount

이 API 는 컴포넌트가 화면에 나타나게 되었을 때 호출된다. DOM 을 사용해야하는 외부 라이브러리를 연동하거나 해당 컴포넌트에서 필요로 하는 데이터를 요청하기 위해 axios, fetch 등 ajax 요청을 하거나 DOM 의 속성을 읽거나 직접 변경하는 작업을 진행한다.

## 2. 컴포넌트 업데이트

컴포넌트 업데이트는 props 의 변화, 그리고 state 의 변화에 따라 결정된다. 업데이트가 되기 전과 그리고 된 후에 어떠한 API 가 호출 되는지 살펴보자.

### componentWillReceiveProps

이 API 는 컴포넌트가 새로운 props 를 받게 됐을 떄 호출된다. 이 안에서는 주로, state 가 prop 에 따라 변해야 하는 로직을 작성한다. 새로 받게될 props 는 nextProps 로 조회할 수 있으며, 이때 this.props 를 조회하면 업데이트 되기 전의 API 이니 조심해야한다. 마찬가지로 지금은 deprecated 중이다. 이 기능은 상황에 따라 getDerivedStateFromProps 가 대체한다.

### [NEW] static getDerivedStateFromProps()

이 API 는 props 로 받아온 값을 state 로 동기화 하는 작업을 해줘야 하는 경우에 사용된다.

> null 을 리턴하면 따로 업데이트 할것이 없다는 의미이다.

### shouldComponentUpdate

이 API 는 컴포넌트를 최적화하는 작업에서 매우 유용하게 쓰인다. 우리가 저번에 배웠을 때, React 에서는 변화가 발생하면 부분만 업데이트해줘서 성능이 좋다고 하는데 사실 변화가 발생한 부분만 감지하기 위해서는 Virtual DOM 에 한번 그려줘야 한다.

즉, 현재 컴포넌트의 상태가 업데이트되지 않아도 부모 컴포넌트가 리렌더링 되면, 자식 컴포넌트들도 렌더링된다. 여기서 렌더링은 render()의 호출이다.

변화가 없으면 물론 DOM 조작은 하지 않게 된다. 그저 Virtual DOM 에만 렌더링 될 뿐이다. 이 작업은 부하가 많은 작업은 아니지만 컴포넌트가 무수히 많이 렌더링된다면 이야기는 달라진다. 쓸데없이 낭비되는 이 CPU 처리량을 줄여주는 역할을 shouldComponentUpdate 가 불필요한 경우엔 리렌더링을 방비하기 위해 활동한다.

### componentWillUpdate

여기선 주로 애니메이션 효과를 초기화하거나, 이벤트 리스너를 없애는 작업을 하는데 마찬가지로 deprecated 중이다.

### [NEW]getSnapshotBeforeUpdate()

이 API 가 발생하는 시점은 다음과 같다.

1. render()
2. getSnapshotBeforeUpdatae()
3. 실제 DOM 에 변화발생
4. componentDidUpdat

이 API 를 통해서, DOM 변화가 일어나기 직전의 DOM 상태를 가져오고 여기서 리턴하는 값은 componentDidUpdate 에서 가져오면 된다.

```js
getSnapshotBeforeUpdate(preVProps, prevState){
  if (prevState.array !== this.state.array){
    const {scrollTop, scrollHeight} = this.list;
  }
  // 여기서 반환되는 값이 snapshot이다.
  return {
    scrollTop, scrollHeight
  }
}

componentDidUpdate(prevProps, prevState, snapshot){
  if(snapshot){
    const {scrollTop} = this.list;
    if (scrollTop !==snapshot.scrollTop) return; // 기능이 이미 구현되어 있다면 처리하지 않음
    const diff = this.list.scrollHeight - snapshot.scrollHeight;
    thist.list.scrollTop +=diff;
  }
}
```

### componentDidUpdate

이 API 는 커포넌트에서 render()를 호출하고 난 다음에 발생하게 된다. 이 시점에선 this.props 와 this.state 가 바뀌었다. 그리고 파라미터를 통해 이전의 값인 prevProps 와 prevState 를 조회할 수 있다. 그리고 getSnapshotBeforeUpdate 에서 변환한 snapshot 값은 세번째 값으로 받아온다.

## 3. 컴포넌트 제거

### componentWillUnmount

> will 친구들 중에 마지막으로 살아남은 친구이다

컴포넌트가 더이상 필요하지 않다면 이벤트를 제거하고 외부라이브러리를 사용한 것이 있다면 dispose 해주는 등 여기서 호출해주면 된다.

## 컴포넌트에 에러 발생

render 함수에서 에러가 발생한다면, 리액트 앱이 크래쉬 된다. 그러한 상황에 유용하게 사용할 수 있는 API 는 componentDidCatch 이다

### componentDidCatch

```js
componentDidCatch(error, info){
  this.setState({
    error : true
  })
}
```

에러가 발생하면 이런식으로 componentDidCatch 가 실행되게 되고 render 함수쪽에서 이에따라 에러를 띄어주면 된다.

이 API 를 사용하게 될시 컴포넌트 자신의 render 함수에서 발생해버리는 것은 잡아낼 수는 없지만, 그 대신에 컴포넌트의 자식 컴포넌트 내부에서 발생하는 에러들을 잡아낼 수 있다.
