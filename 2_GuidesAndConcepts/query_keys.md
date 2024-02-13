[👉🏻 공식문서 바로가기](https://tanstack.com/query/v5/docs/framework/react/guides/query-keys)

### Query Keys

Tanstack Query의 핵심은 쿼리 키를 기반으로 쿼리 캐싱을 자동으로 관리하는 것이다. 쿼리 키는 최상위 레벨에서 배열이여야 하며, 하나의 문자열만 있는 배열이거나 여러 문자열과 중첩된 객체가 있는 배열과 같이 단순화할 수도 있다.
쿼리 키가 serializable 될 수 있고, 쿼리 데이터에 대해 고유하다면, 사용할 수 있다.

### Simple Query Keys

key의 가장 간단한 형태는 상수 value로 된 배열이다. 이 포맷은 사용된다 :

- 일반적인 목록 / index resource
- 계층적이지 않은 resource

```jsx
// A list of todos
useQuery({ queryKey: ['todos'], ... })

// Something else, whatever!
useQuery({ queryKey: ['something', 'special'], ... })
```

### Array Keys with variables

쿼리가 데이터를 고유하게 설명하기 위해 더 많은 정보가 필요한 경우, 이를 설명하기 위해 문자열과 serializable 한 객체를 포함하는 배열을 사용할 수 있다. 다음에 사용된다 :

- 계층적이거나 중첩된 resource
  - 항목을 고유하게 식별하기 위해 ID, 인덱스 또는 기타 원시 값을 전달하는 것이 일반적이다.
- 추가 매개변수를 사용하는 쿼리
  - 추가 옵션 객체를 전달하는 것이 일반적이다.

```jsx
// An individual todo
useQuery({ queryKey: ['todo', 5], ... })

// An individual todo in a "preview" format
useQuery({ queryKey: ['todo', 5, { preview: true }], ...})

// A list of todos that are "done"
useQuery({ queryKey: ['todos', { type: 'done' }], ... })
```

### Query Keys는 결정론적으로 해싱된다.

이는 객체 내 키의 순서에 관계없이 다음과 같은 모든 쿼리가 동등하게 간주된다는 것을 의미한다 :

```jsx
useQuery({ queryKey: ['todos', { status, page }], ... })
useQuery({ queryKey: ['todos', { page, status }], ...})
useQuery({ queryKey: ['todos', { page, status, other: undefined }], ... })
```

그러나, 다음 쿼리 키는 서로 동등하지 않다. 배열 항목의 순서가 중요하다!

```jsx
useQuery({ queryKey: ['todos', status, page], ... })
useQuery({ queryKey: ['todos', page, status], ...})
useQuery({ queryKey: ['todos', undefined, page, status], ...})
```

### 만약 당신의 쿼리 함수가 변수의 의존한다면, 해당 변수를 쿼리 키에 포함하라.

쿼리 키는 검색하는 데이터를 고유하게 설명하기 때문에 쿼리 함수에서 사용하는 변경되는 모든 변수를 포함해야 한다. 예를들어 :

```jsx
function Todos({ todoId }) {
  const result = useQuery({
    queryKey: ["todos", todoId],
    queryFn: () => fetchTodoById(todoId),
  });
}
```

쿼리 키는 쿼리 함수의 종속성으로 작용한다. 쿼리 키에 종속 변수를 추가하면 쿼리가 독립적으로 캐시되고, 변수가 변경될 때마다 (staleTime 설정에 따라) 쿼리가 자동으로 다시 가져온다. 자세한 내용 및 예제는 exhaustive-deps 섹션을 참조할 것.

### 추가적으로

더 큰 애플리케이션에서 쿼리 키를 구성하는 팁은 Effective React Query Keys를 참조하고 커류니티 리소스의 Query Key Factory 패키지를 확인할 것

_c.f. 전체 페이지 캡쳐화면_
![이미지](/img/day5.jpg)
