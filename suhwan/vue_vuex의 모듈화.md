## 왜 모듈화를 해야하는가?

서비스 규모가 커질 수록 상태관리를 하나의 js파일에서 하기가 복잡해진다.

## 모듈이란?

module이란 자체적으로 action, mutation, getter, state, module을 갖는 store의 하위 객체를 의미. 

![](/Users/kwan/Library/Application%20Support/marktext/images/2022-08-05-00-49-38-image.png)

## 모듈화하는 방식

파일을 분리하는 방식은 하기 나름이다.

## 주의할 점

state를 제외한 action, mutation, getter는 모듈화해서 작성해도 똑같이 동작하는 것을 확인할 수 있다.

### module의 state

- state는 module의 local state
- getter나 mutation에서 전달받는 state는 모듈의 state
- mapstate 사용법

```jsx
...mapState('모듈 명', ['state 이름']) // namespace 사용해야함.
```

### module의 mutation

- module의 mutation에서는 전달된 state를 통해 모듈의 데이터만 수정가능하다.
- root store에 접근할 방법이 없다. 즉, module의 state만 변경가능하고 root의 state는 읽기만 가능하다.

### module의 action

- 특별히 선언하지 않는 한 actions, mutations, getter는 store에서 통합관리됨.
- module의 action에서 commit을 통해 mutation을 호출하면 root store의 mutation 호출됨.

## namespace

기본적으로는 module의 action, mutation, getter는 전역 namespace에서 관리된다.

그래서 만약 이름이 겹친다면 getter는 에러를, mutation이나 action은 모든 함수를 호출한다.

따라서, 이름을 모두 다르게하거나, namespace를 사용해야한다.

```jsx
export const foo={
namespaced: true, // 모듈 상단에 입력해주자
                                 // namespace 사용시 호출: ("모듈명"("foo") / "호출함수명")
}
```

## mapHelper

### mapState

![](/Users/kwan/Library/Application%20Support/marktext/images/2022-08-05-00-50-00-image.png)

mapState는 state를 바로 참조하는 것이 아니라 getter함수를 생성해 준다는 것을 알 수 있다.

state값으로 계산을 할때는 getter를 사용, 변경할때는 mutation을 사용한다.


