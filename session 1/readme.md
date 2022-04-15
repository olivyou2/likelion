# Session1 자기소개 페이지

## 위에있는 잡다한 것들
아주 열심히 css 랑 html 로 뚝딱뚝딱 했습니다.

## 아래있는 게임

### About Game
어떤 기능을 추가할까 고심한 끝에 테마변경 / 간단한 게임을 제작해야 겠다고 결론을 내렸습니다. 간단한 플랫포머 게임이고, 사실 게임이라고 할만한것이 없는게, 움직이고 점프를 뛰는 것 밖에 구현되어있지 않습니다.

### About Render Cycle
<img src="https://user-images.githubusercontent.com/36305517/163560551-d6849212-f296-497f-8729-8e35dc91fcc7.png" width="100">

목표 프레임은 60으로 고정되어 있으며, Object Render -> UI Render -> View Render 의 순서로 진행합니다. UIObject 의 Property 들은 State Watch 로 관리되어 필요없는 렌더링을 방지합니다. 또 기존의 Canvas 에 ctx 를 통한 렌더링 방식이 아닌, Html 의 Element 를 조작하여 렌더링을 진행합니다. 이에 따라 사용자 브라우저의 Graphics 성능이 게임의 성능을 좌우합니다.

### About Object Render
<img src="https://user-images.githubusercontent.com/36305517/163561442-b650d9df-2c0a-45e8-90a7-5476d2b96c7f.png" width="300">

오브젝트 렌더링 중, Before-Step 사용자 정의 이벤트가 실행되고, Internal 렌더링이 진행된 후, Step 사용자 정의 이벤트가 실행됩니다. Internal 렌더링에서는, View 를 적용한 스크린 상에서의 좌표를 계산하고, 물리연산을 진행한 후 css 값들을 변경합니다. 스크린 상에서의 좌표는 다음과 같은 코드를 통해 얻을 수 있습니다. 
~~~~js
const object = createObject();

// ~

const viewX = object.getViewX();
const viewY = object.getViewY();
~~~~
모든 event 들은 브라우저상의 action이 prevent 되며, 모든 이벤트들은 Render Cycle 과 무관하게 브라우저 AddEventListner 의 내부에서 호출됩니다. 다음과 같은 eventListener 가 있습니다:
~~~~js
const object = createObject();

// ~

object.keyDown = () => {};
// 키 입력 시작

object.keyUp = () => {};
// 키 입력 끝

object.beforeStep = () => {};
// 렌더링 전
/*
UIObject 와 GameObject 의 좌표를 Synchronization 하는 작업은 beforeStep 에서 수행하는 것이 좋습니다. 
step 에서 작업을 수행하게 되면, 2회 렌더링에 걸쳐 두 좌표의 Synchronization 작업이 진행되어 부자연스러움이 발생할 수 있습니다.
*/

object.step = () => {};
// 렌더링 후

~~~~

또 모든 변수들은 Javascript 엔진에 의해 관리됩니다. Chrome 의 경우, 변수들의 참조가 사라지거나 혹은 다른 이유로, V8 엔진의 GC Collector 에 의해 강제 해제될 수 있습니다. 또한 인스턴스화 되는 오브젝트들이 같은 변수를 공유하게 되는 문제가 발생할 수 있습니다. 이 Game Engine 은 object 에 의해 참조되는 Internal Hash 를 제공합니다:
~~~~js
const object = createObject();

// ~

object.vals.a = 10;
object.vals.b = 20;

// 또한 UIObject 렌더링에 사용되는 useState 를 일반 오브젝트 에서도 사용할 수 있습니다:

object.vals.state1 = useState(10);

// 다음과 같이 state 를 변경할 수 있습니다.
object.vals.state1.setState(15);

// 또는,

object.vals.state1.state = 15;
// 이런 방식으로도 state 의 watch 를 발생시킬 수 있으나, 개발편의상 사용하지 않을것을 권장합니다.
~~~~

### About View System
내부적으로 View System 이 적용되어 있습니다. 
~~~~js
const object = createObject();

setViewPortChase(object);
~~~~
이런 방식으로 Game Engine 에서 제공하는 Object Chasing 을 사용할 수 있으나, 다음과 같은 식으로 개발자가 직접 Chasing System 을 구현할 수 있습니다.
~~~~js
const {x, y, width, height} = getViewPort();
// 현재 View 의 좌표와 크기를 받아옵니다.

setViewPort(x, y, width, height);
// View 의 좌표와 크기를 변경합니다.

/*
viewPort 의 width 와 height 는 게임 스크린의 사이즈에 관여하지 않습니다. 그러나 내부적으로 제공하는 Object Chasing 에 영향을 주
*/
~~~~

### 아 쓰기 귀찮다
~~~~js

~~~~
