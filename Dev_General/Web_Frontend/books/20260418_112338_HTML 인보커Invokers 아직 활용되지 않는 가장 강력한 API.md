# 번역 기사

- **원본 URL:** https://javascript.plainenglish.io/html-invokers-the-coolest-api-you-arent-using-yet-e78c3ddee927
- **번역 일시:** 2026-04-18 11:23:38

---

# HTML 인보커(Invokers): 아직 활용되지 않는 가장 강력한 API

인보커(Invokers)는 여러분이 아직 사용하고 있지 않은 가장 멋진 HTML API입니다. 이 기능은 이제 모든 주요 브라우저에 곧 출시될 예정입니다. 현재는 Safari Technology Preview, Firefox, Chrome에서 이미 사용 가능합니다. 정말 멋진 기능이죠.

그렇다면 인보커란 무엇일까요?

## 핵심 아이디어

인보커는 버튼이 자바스크립트(JavaScript) 없이도 실제로 작동하게 해줍니다. 버튼에 어떤 요소를 제어하고 무엇을 할지 알려주기만 하면 됩니다. 그게 전부입니다.

이런 기능이 이제까지 없었다는 게 놀랍습니다.

정확한 코드 예시는 다음과 같습니다.

### 기존 방식

현재, 버튼으로 다이얼로그(Dialog)를 열려면 다음과 같은 코드를 작성해야 합니다.

```javascript
const button = document.querySelector('#open-button');
const dialog = document.querySelector('#my-dialog');
button.addEventListener('click', () => {
  dialog.showModal();
});
```

작동은 하지만, "버튼이 무언가를 연다"는 단순한 동작치고는 너무 많은 절차가 필요합니다.

### 인보커 작동 방식 (새로운 방식)

버튼에 두 가지 속성(attribute)만 추가하면 끝입니다.

```html
<button commandfor="my-dialog" command="show-modal">
  Open Dialog
</button>
<dialog id="my-dialog">
  <h2>Welcome!</h2>
  <p>This dialog opened without a single line of JavaScript.</p>
  <button commandfor="my-dialog" command="close">
    Close This
  </button>
</dialog>
```

이게 전부입니다. 버튼은 그저 무엇을 해야 할지 알고 있습니다. 자바스크립트 파일도, 이벤트 리스너(event listener)도, DOM(Document Object Model) 쿼리도 필요 없습니다. 정말 아름답습니다.

### 실제 사용 예시

실제로 만들 수 있는 예시입니다. 팝오버(Popover)가 있는 설정 메뉴입니다.

```html
<button commandfor="settings-menu" command="toggle-popover">
  ⚙️ Settings
</button>
<div id="settings-menu" popover>
  <h3>Settings</h3>
  <label>
    <input type="checkbox"> Dark Mode
  </label>
  <label>
    <input type="checkbox"> Notifications
  </label>
  <label>
    <input type="checkbox"> Auto-save
  </label>
  <button commandfor="settings-menu" command="hide-popover">
    Done
  </button>
</div>
```

설정 버튼을 클릭하면 메뉴가 나타나고, 'Done'을 클릭하면 닫힙니다. 자바스크립트가 전혀 필요 없습니다. 이전에는 20줄 이상의 코드가 필요했습니다.

### 속성(Attributes)

이 마법을 가능하게 하는 두 가지 속성이 있습니다.

*   `commandfor` - 제어하려는 요소의 ID를 가리킵니다. 예를 들어, `id="my-dialog"`인 다이얼로그를 제어하려면 `commandfor="my-dialog"`라고 작성합니다.
*   `command` - 발생시키려는 동작을 지정합니다. 다이얼로그를 열려면 `show-modal`, 닫으려면 `close`, 팝오버의 경우 `toggle-popover` 등이 있습니다.

말 그대로 알아야 할 전부입니다.

### 제어 가능한 요소

현재 다음 요소들을 제어할 수 있습니다.

*   **다이얼로그(Dialogs):** `show-modal`과 `close`는 즉시 작동합니다. 드디어!
*   **팝오버(Popovers):** `show-popover`, `hide-popover`, `toggle-popover`. 매우 깔끔합니다.
*   **디테일스 요소(Details Elements):** 아코디언(accordion) 스타일 콘텐츠를 위한 `open`, `close`, `toggle`입니다.

더 많은 기능이 추가될 예정입니다. 파일 입력(file inputs), 비디오 컨트롤(video controls) 등 모든 것이 곧 지원됩니다.

### 커스텀 명령어(Custom Commands)도 멋집니다.

자신만의 명령어를 만들 수 있습니다. 명령어는 두 개의 하이픈(`--`)으로 시작하기만 하면 됩니다.

```html
<button commandfor="photo" command="--flip-horizontal">
  Flip Photo
</button>
<button commandfor="photo" command="--rotate-90">
  Rotate 90°
</button>
<img id="photo" src="vacation.jpg" alt="Beach photo">
```

그런 다음, 이 커스텀 명령어를 수신하고 원하는 작업을 수행하는 간단한 자바스크립트 코드를 작성합니다.

```javascript
document.getElementById('photo').addEventListener('command', (e) => {
  if (e.command === '--flip-horizontal') {
    e.target.style.transform = 'scaleX(-1)';
  } else if (e.command === '--rotate-90') {
    e.target.style.transform = 'rotate(90deg)';
  }
});
```

하지만 클릭 처리(click-handling) 설정은 이미 완료되어 있어 편리합니다.

### 또 다른 확실한 예시: 이미지 갤러리

다음/이전 버튼이 있는 완전한 이미지 갤러리입니다.

```html
<div class="gallery">
  <button commandfor="gallery-dialog" command="show-modal">
    📷 View Gallery
  </button>
</div>
<dialog id="gallery-dialog">
  <div class="gallery-content">
    <img id="current-image" src="photo-1.jpg" alt="Gallery photo">
    
    <div class="controls">
      <button commandfor="current-image" command="--previous">
        ← Previous
      </button>
      <button commandfor="current-image" command="--next">
        Next →
      </button>
      <button commandfor="gallery-dialog" command="close">
        ✕ Close
      </button>
    </div>
  </div>
</dialog>
```

이 다이얼로그는 내장된 명령어(built-in commands)로 열고 닫힙니다. 다음/이전 버튼은 몇 줄의 자바스크립트 코드로 연결할 수 있는 커스텀 명령어(custom commands)를 사용합니다. 하지만 버튼 클릭 관련 메커니즘은 이미 처리되어 있습니다.

### 현재 브라우저 지원 현황

현재 상황은 다음과 같습니다.

*   **Chrome Canary 134+:** 실험용 플래그(experimental flag)를 켜야 합니다.
*   **Firefox Nightly 135+:** 플래그를 활성화해야 합니다.
*   **Safari Technology Preview:** 플래그를 켜야 합니다.

### 왜 이것이 실제로 중요한가

이것이 세상을 바꾸는 대단한 기능은 아닙니다. 하지만 수년간 웹 개발자들을 괴롭혀왔던 문제를 해결해줍니다.

버튼이 다른 요소를 제어하는 것은 웹의 기본 기능입니다. 이를 위해 라이브러리를 가져오거나 상용구 코드(boilerplate code)를 작성할 필요가 없어야 합니다. HTML이 직접 처리해야 하며, 이제 그렇게 됩니다.

또한, 기능이 플랫폼(platform)에 내장되면 접근성(accessibility)이 자동으로 확보됩니다. 스크린 리더(screen reader)가 더 잘 작동하고, 키보드 내비게이션(keyboard navigation)도 그대로 작동합니다. 모든 ARIA 속성(attributes)을 일일이 추가하는 것을 기억할 필요가 없습니다.

### 솔직히 말해서

이 기능은 "잠깐, 이게 원래 없던 거였어?"라고 생각하게 만드는 기능 중 하나입니다. 왜냐하면 일단 보면 너무나도 당연하게 느껴지기 때문입니다.

웹 플랫폼(web platform)은 수년 동안 자바스크립트 프레임워크(JavaScript frameworks)로 해왔던 작업을 마침내 따라잡고 있습니다. 이는 배포할 자바스크립트가 줄어들고, 유지보수할 코드가 줄어들며, 웹사이트가 더 잘 작동한다는 점에서 매우 긍정적입니다.

인보커는 간단하고, 우아하며, 곧 모든 곳에서 사용될 것입니다. 수많은 이벤트 리스너를 삭제할 준비를 하세요.

### 결론

HTML 인보커(Invokers)는 버튼이 자바스크립트(JavaScript) 없이도 기능을 제어하게 해줍니다. 두 가지 속성(attribute)만 있으면 됩니다. 라이브러리도 필요 없습니다. 깔끔하고, 간단하며, 곧 여러분이 사용하는 브라우저에 적용될 것입니다.

이는 웹 개발을 조금 더 즐겁게 만드는 종류의 개선입니다. 솔직히 말해, 이런 개선이 더 많이 필요합니다.