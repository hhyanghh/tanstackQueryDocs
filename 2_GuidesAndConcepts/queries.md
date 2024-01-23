[👉🏻 공식문서 바로가기](https://tanstack.com/query/v5/docs/react/overview)

## Query Basics 쿼리 기본

쿼리는 고유한 키에 연결된 비동기 데이터 소스에 대한 선언적 dependency 이다. 쿼리는 서버에서 데이터를 가져오기 위해 어떠한 Promise 기반 method(GET POST 포함)와도 함께 사용할 수 있다. 만약 당신의 메서드가 서버에서 데이터를 수정한다면, Mutations를 사용하는걸 권장한다.

컴포넌트나 custom hook에서 쿼리를 구독하려면, at least `useQuery` 훅을 호출하라.

- 쿼리를 위한 고유한 key
- Promise를 반환하는 함수:
  - Resolves the data,
  - 에러를 발생시키거나

```jsx
import { useQuery } from "@tanstack/react-query";

function App() {
  const info = useQuery({ queryKey: ["todos"], queryFn: fetchTodoList });
}
```

당신이 제공한 unique key는 내부적으로 refetching, 캐싱 및 애플리케이션 전반에 걸쳐 쿼리를 공유하는데 사용된다.

‘`useQuery`’로 반환된 쿼리 result에는 templating 및 데이터의 다른 사용에 필요한 모든 정보가 포함되어 있다.

```jsx
const result = useQuery({ queryKey: ["todos"], queryFn: fetchTodoList });
```

‘`result`’ 객체에는 productive 생산적으로 사용하기 위해 주의해야할 매우 중요한 상태가 포함되어 있다.

- `isPending` or “status === pending’” - 쿼리가 아직 data를 가지고 있지 않음
- `isError` or “status === ‘error’ “ - 쿼리에서 오류가 발생하다.
- `isSuccess` or “status === ‘success’ “ - 쿼리가 성공하고 데이터가 사용 가능하다.

이러한 주요 상태 이외에도 쿼리의 상태에 따라 더 많은 정보가 제공된다.

- ‘`error`’ - 만약 쿼리가 ‘isError’ 상태라면, 그 에러는 error 속성을 통해 사용 가능하다.
- ‘`data`’ - 만약 쿼리가 ‘isSuccess’ 상태라면, 그 데이터는 ‘data’ property를 통해 이용 가능하다.
- ‘`isFetching`’ - 어떠한 상태에서든 쿼리가 언제든지 데이터를 가져오고 있는 경우 (background에서 refetching 포함), isFetching 은 true 이다.

대부분의 쿼리에 대해서는 보통 isPending 상태를 확인한 후에, isError 상태를 확인하고, 마침내 마지막으로 데이터가 사용 가능하다고 가정하고 성공 상태를 렌더링하는 것이 assume 충분하다.

```jsx
function Todos() {
  const { isPending, isError, data, error } = useQuery({
    queryKey: ["todos"],
    queryFn: fetchTodoList,
  });

  if (isPending) {
    return <span>Loading...</span>;
  }

  if (isError) {
    return <span>Error: {error.message}</span>;
  }

  // We can assume by this point that `isSuccess === true`
  return (
    <ul>
      {data.map((todo) => (
        <li key={todo.id}>{todo.title}</li>
      ))}
    </ul>
  );
}
```

만약, booleans가 마음에 들지 않는다면, 언제든지 ‘status’ 상태를 사용할 수도 있다.

```jsx
function Todos() {
  const { status, data, error } = useQuery({
    queryKey: ["todos"],
    queryFn: fetchTodoList,
  });

  if (status === "pending") {
    return <span>Loading...</span>;
  }

  if (status === "error") {
    return <span>Error: {error.message}</span>;
  }

  // also status === 'success', but "else" logic works, too
  return (
    <ul>
      {data.map((todo) => (
        <li key={todo.id}>{todo.title}</li>
      ))}
    </ul>
  );
}
```

TypeScript는 ‘data’에 access 접근하기 전에 pending 및 error를 확인했다면 데이터의 타입을 정확하게 좁힌다.

## FetchStatus

status 필드 외에도 다음 옵션을 가진 추가적인 fetchStatus 속성을 얻을 수 있다.

- `fetchStatus === 'fetching'` - 현재 쿼리가 패칭 중이다.
- `fetchStatus === 'paused'` - 쿼리가 fetch를 하려고했지만, 일시 중단되었다. 자세한 내용은 Netword Mode 가이드에서 확인하기
- `fetchStatus === 'idle'` - 현재 쿼리는 아무 작업도 수행하고 있지 않다.

## Why two different status? 왜 다른 두개의 status?

백그라운드에서 refetch와 stale-while-revalidate 로직은 모든 status 및 fetchStatus 조합이 가능하게 합니다. 예를들면 :

- success 상태의 쿼리는 일반적으로 idle fetchStatus에 있을 것이지만, 백그라운드에서 refetch가 발생하는 경우 fetching 에 있을 수도 있다.
- 마운트는 되었는데 데이터가 없는 쿼리는 보통 ‘pending’ 상태와 ‘fetching’ fetchStatus 있을 것이지만, 네트워크 연결이 없는 경우 paused 상태에 있을 수도 있다.

따라서, 그러니 쿼리가 데이터를 실제로 가져오지 않고도 pending 상태에 있을 수 있다는 것을 명심 염두하라. As a rule of thumb: 일반적인 지침으로는:

- ‘status’ 는 ‘data’에 대한 정보를 준다. : Do we have any or not? 데이터가 있는지 여부
- ‘fetchStatus’ 는 ‘queryFn’에 대한 정보를 준다. : Is it running or not? 실행 중인지 아닌지

_c.f. 전체 페이지 캡쳐화면_
![이미지](/img/day4.jpg)
