# 📝 Toast, Modal 개발
본격적으로 페이지의 기능을 구현하기 전에 팀원들 각자 공용컴포넌트를 먼저 개발하기로 정했고, 내가 담당했던 것은 Toast, Modal이 었기 때문에 바로 작업을 시작했다.

## 💡 초안
초기에 만든 Toast, Modal 컴포넌트는 단순했다.

그냥 하나의 공용컴포넌트로 제작하고, 부모 페이지 컴포넌트에서 제어해주는 식으로 구현하면 되겠지 하는 마음으로 일단 만들어 보았다.

```js
const Modal = () => {
  return ...
}

export default Modal;
```

```js
//부모 컴포넌트
import Modal from './Modal';
const PageComponent = () => {
  const [isModalOpen, setIsModalOpen] = useState(false);

  return (
    ...
    {isOpen && <Modal/>
  )
}
```

이렇게 만들어 놓으니 2가지 문제가 있었다.

1. 부모에서 Modal과 Toast를 사용하기 위해서 isModalOpen과 같은 state를 일일이 제어를 해줘야 함
2. 부모 컴포넌트의 return문에 Modal과 Toast를 위치 시켜야한다는 점

그러니까, 역할 분리가 제대로 이루어지지 않는다고 판단했다.

## 💡 개선
현재의 문제점을 개선하기 위해 어떤 방식을 적용할 지 고민을 해 보았다.
1. 모든 컴포넌트에서 showModal, showToast 등의 함수를 이용해 제어가 가능해야함
2. 역할 분리를 명확하게
   - Modal, Toast 에서는 디자인만
   - 부모컴포넌트에서는 Modal 호출만

그럼 여기서 Modal과 Toast를 열고 닫는 함수를 관리하며 z-index를 관리하는 등 종합적인 동작을 관리하는 컴포넌트가 필요하다고 생각했다.

### ❗ Context의 활용
이 문제들을 해결하기 위해 Context를 사용하는 방법을 선택했다.

#### ModalProvider.jsx
```js
export const ModalContext = createContext();

export const ModalProvider = () => {
  const [modal,setModal] = useState(null);
  const [isOpen,setIsOpen] = useState(false);
  const showModal = (ModalComponent) => {
    setIsOpen(true);
    setModal(ModalComponent);
  };
  const closeModal = () => { setIsOpen(false) };

  return (
    <ModalContext.Provider value={ showModal, closeModal }>
      {children}
      {isOpen &&
      createPortal(
        <div className='modal-background'>
          <div className='modal-wrapper'>
            {modal}
          </div>
        </div>
      ,document.getElementById('modal-root')}
    </ModalContext.Provider>
  )
}
```
#### useModal.jsx
```js
import { useContext } from 'react';
import { ModalContext } from '../contexts/ModalProvider';

export const useModal = () => {
  const context = useContext(ModalContext);
  if (!context) throw new Error('useModal은 ModalProvider 안에서 사용되어야 합니다');
  return context;
};
```

#### PageComponent.jsx
```js
import { useModal } from '@/hooks/useModal';
const PageComponent = () => {

  const { showModal, closeModal } = useModal();

  const handleModalOpen = () => {
    showModal(<Modal />);
  }

  return (
    
  );
}
```

1. 모든 컴포넌트는 ModalProvider 하위에 위치하도록 한다.
2. ModalProvider에서 Modal의 Open/Close를 제어한다.
3. 실제 Modal을 사용하는 컴포넌트에서는 `showModal`, `closeModal` 함수를 호출하여 제어하도록 만든다.
4. 어떤 Modal을 사용할 지는 실제 사용하는 컴포넌트에서 자유롭게 선택할 수 있도록 만든다.


<br></br>

---

## 📜 트러블슈팅 기록

### ❗ Modal closing 구간에서 조작이 불가능한 오류 수정
[[🔗 [Hotfixes] Modal 버그 수정]](https://github.com/codeit-fe16-part2-team2/project-rolling-app/pull/47/files)
처음 Modal 컴포넌트를 만들때 fade-in, fade-out 애니메이션을 적용하기 위해서

isClosing이라는 state를 관리해 이 값이 true이면 사라지는 애니메이션을 보여주고, isClosing state가 true가 되면 일정 시간이 지난 후 isOpen state를 false로 만들어 Modal이 사라지게 만들었었다.

Modal이 띄워질때 배경도 전체 구역을 차지하면서 나타나는데, 이 구역 때문에 Modal이 꺼지는 동안에는 외부 클릭 등 조작이 불가능해서 사용자 경험면에서 문제가 있을거라고 판단했다.

해결 방법은 2가지로 나누어서 접근했다.

---

1. fix: modal이 종료되는 시간 동안 다른 아이템 클릭이 안되는 오류 수정

`isClosing` state가 true 일 때 Modal Background className에 isClosing을 추가했고, isClosing style 클래스에
아래와 같이 `pointer-events: none;` 스타일을 적용해 Modal이 꺼지는 동안, 그러니까 꺼지기 시작하고 완전히 꺼지기 직전 사이에서도 웹페이지 조작이 가능하도록 만들었다.

```js
  &.isClosing {
    animation: fade-out-up 0.4s ease forwards;
		pointer-events: none;
  }
```

이렇게 수정하니 두번째 문제가 발생했다.

isClosing state가 true로 바뀔 때 isOpen을 true로 바꾸는 Timer가 실행 되는데,

isClosing state가 true인 구간에서 다른 아이템을 선택해 Modal이 다시 열리면 setTimeout 함수가 초기화되지 않고 아직 살아있기 때문에 마지막으로 연 Modal이 열리다가 갑자기 꺼져 버리는 문제가 발생했다.

그래서 isClosing이 true인 구간에서 다시 Modal을 열 경우 이 setTimeout함수를 초기화 시키는 구문을 작성할 필요가 있었다.

---

2. fix: Modal의 closing 타이밍에 closeModal 함수의 setTimeout 함수를 강제로 reset하도록 수정

useRef를 이용해 setTimeout 객체를 저장하고, Modal이 close 됐다가 다시 Open될때 useRef에 저장된 타이머 정보를 불러와 초기화 시켜버리는 방법을 사용하였다.
```js
 const closeTimeoutRef = useRef(null);

  const showModal = (modalItems) => {
    if (closeTimeoutRef.current) {
      clearTimeout(closeTimeoutRef.current);
      closeTimeoutRef.current = null;
      setIsClosing(false);
      setIsOpen(false);
    }
    setModal(modalItems);
    setIsOpen(true);
  };

  const closeModal = () => {
    setIsClosing(true);
    closeTimeoutRef.current = setTimeout(() => {
      setIsOpen(false);
      setIsClosing(false);
      closeTimeoutRef.current = null;
    }, 300);
  };
```
<br></br>

---

### ❗ Modal이 열린 상태에서 다른 페이지로 이동할 때 Modal이 계속 열려있는 오류 수정
Modal이 열려있는 상태에서 다른 페이지로 이동할 때(location이 변할 때) `closeModalImmediately` 함수를 실행하여 강제로 close하는 구문을 추가하였다.



```js
  const closeModalImmediately = () => {
    setIsOpen(false);
  };

  useEffect(() => {
    if (!isOpen) return;
    closeModalImmediately();
  }, [location]);


```

### ❗ Modal 내부에서 클릭한 상태에서 Modal 외부로 드래그 후 클릭 해제하면 Modal이 close 되는 오류 수정
MouseDown 될때의 컴포넌트 정보를 저장하고, MouseUp 될때의 컴포넌트 정보를 저장하여 둘다 Modal 컴포넌트 외부에서 이벤트가 발생했을 경우에만 Modal이 close되도록 수정하였다.

```js
useEffect(() => {
    if (!isOpen) return;
    const handleMouseDown = (e) => {
      if (modalWrapperRef.current?.contains(e.target)) {
        isMouseDownInsideModal.current = true;
      } else {
        isMouseDownInsideModal.current = false;
      }
    };

    const handleMouseUp = (e) => {
      if (
        !modalWrapperRef.current?.contains(e.target) &&
        isMouseDownInsideModal.current === false
      ) {
        closeModal();
      }
    };

    document.addEventListener('mousedown', handleMouseDown);
    document.addEventListener('mouseup', handleMouseUp);

    return () => {
      document.removeEventListener('mousedown', handleMouseDown);
      document.removeEventListener('mouseup', handleMouseUp);
    };
  }, [isOpen]);

```
