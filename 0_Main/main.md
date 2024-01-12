[👉🏻 공식문서 바로가기](https://tanstack.com/query/v5)

## TaaStack Query v5

> "TS/JS, React, Solid, Vue 및 Svelte를 위한 강력한 비동기 상태 관리"

작은 단위의 상태관리, manual 리패칭 그리고 끊임없는 비동기 스파게티 코드는 던지세요,
TanStack Query는 선언적이고 always-up-to-date(항상 최신) auto-managed(자동 관리) 쿼리 및 뮤테이션을 제공하여 개발자와 사용자 경험을 직접 향상시킨다.

### 1. DECLARATIVE & AUTOMATIC (선언적 및 자동화)

---

데이터 검색 로직을 수동으로 작성하는건 이제 끝났다.
TanStack Query에게 데이터를 어디서 가져오고 얼마나 신선해야 하는지 알려주면, 나머지는 자동으로 처리된다.
zero-configuration으로 캐싱, 백그라운드 업데이트, 그리고 stale 데이터 처리를 쉽게 다룬다.

### 2. SIMPLE & FAMILIAR (간단하고 친숙한)

---

만약 Promise 또는 async/await를 사용하는 방법을 알고있다면, 이미 TanStack Query를 사용하는 방법을 알고 있는 것과 마찬가지다.
관리해야 할 전역 상태, 리듀서, normalization 시스템 또는 이해해야할 무거운 구성이 없다.
간단히 데이터를 resolve 하는 함수를 전달하면 (또는 에러를 던지는) 나머지는 역사이다.

### 3. EXTENSIBLE (확장 가능)

---

TanStack Query는 각 쿼리의 observer instance에 대해 구성 가능하며, 모든 use-case에 맞게 조절할 수 있는 다양한 옵션과 knobs가 제공된다.
전용 devtools, infinite-loading APIs 및 데이터 업데이트를 간편하게 처리하는 최고의 뮤테이션 도구와 함께 제공된다.
걱정마라, 모든것이 성공을 위해 사전적으로 구성되어있다!

---

> "이것은 실제 프로젝트에서 React Query를 사용하는 가장 효과적인 방법이다."
> — Tanner Linsley

✅ 안내되는 방식으로 학습하여 시간을 절약하라. <br />
✅ 실제 애플리케이션을 구축하면서 실전 경험을 쌓아라. <br />
✅ 데이터 fetching 에 대해 다시 걱정하지 말라. <br />

---

**어떠한 종속성도 없다. 모든 기능을 갖추었다.** <br />
어떠한 종속성도 없이, TanStack Query는 밀도있는 feature set을 제공하면서도 매우 경량화되어있다.
주말 취미부터 기업 전자 상거래 시스템 (월마트!)까지, TanStack Query는 creative 속도로 성공할 수 있도록 도와주는 검증된 도구이다.

![이미지2](/img/day1_2.png)

**더 적은 코드, 더 적은 예외사항** <br />
리듀서, 캐싱 로직, 타이머, retry logic, 복잡한 async/await 스크립팅을 작성하는 대신 (계속 나열할 수 있다..), 보통 작성하는 코드의 작은 부분만 작성하면 된다.
TanStack Query를 사용할 떄 작성한 코드 양이 얼마나 적은지 또는 얼마나 많은 코드를 삭제하게 될지 놀라게 될것이다.

_c.f. 전체 페이지 캡쳐화면_
![이미지1](/img/day1_1.png)
