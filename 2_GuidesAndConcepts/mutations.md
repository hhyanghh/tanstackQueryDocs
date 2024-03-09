[👉🏻 공식문서 바로가기](https://tanstack.com/query/v5/docs/framework/react/guides/mutations)

## Mutations 변형, 변경, 변화

쿼리와 달리, mutation(변형)은 일단벅으로 데이터를 생성/업데이트/삭제하거나 서버 side-effects를 수행하는데 사용된다. 이를 위해 TanStack Query는 useMutation hook을 내보낸다.

다음은 서버에 새로운 할 일을 추가하는 mutation의 예 이다.

```tsx
function App() {
  const mutation = useMutation({
    mutationFn: (newTodo) => {
      return axios.post("/todos", newTodo);
    },
  });

  return (
    <div>
      {mutation.isPending ? (
        "Adding todo..."
      ) : (
        <>
          {mutation.isError ? (
            <div>An error occurred: {mutation.error.message}</div>
          ) : null}

          {mutation.isSuccess ? <div>Todo added!</div> : null}

          <button
            onClick={() => {
              mutation.mutate({ id: new Date(), title: "Do Laundry" });
            }}
          >
            Create Todo
          </button>
        </>
      )}
    </div>
  );
}
```

mutation은 특정 순간에 다음 상태 중 하나만 있을 수 있다.

- `isIdle` or `status === 'idle'` : mutation이 현재 idle한 상태이거나 새로 설정/reset된 상태이다.
- `isError` or `status === 'error'` : mutation이 에러에 직면했다.
- `isSuccess` or `status === 'success'` : mutation이 성공했으며 mutation 데이터를 사용할 수 있다.

이러한 기본 상태 외에도 mutation 상태에 따라 더 많은 정보를 사용할 수 있다.

- `error` : 만약 mutation이 오류 상태인 경우 error 속성을 통해 오류를 확인할 수 있다.
- `data` : mutation이 성공 상태인 경우 data 속성을 통해 데이터를 사용할 수 있다.

위의 예에서는 단일 변수나 객체로 mutate 함수를 호출하여 mutations 함수에 변수를 전달할 수 있다는 것도 확인하였다.

변수만 있어도 mutation이 그다지 특별하지는 않지만, onSuceess 옵션, Query Client의 InvalidateQueries 메서드, Query Client의 setQueryData 메서드와 함께 사용하면 mutatin은 매우 강력한 도구가 된다.

\*\* 중요 : mutate 함수는 비동기 함수 이므로 React 16 및 이전 버전의 이벤트 콜백에서 직접 사용할 수 없다. onSubmit에서 이벤트에 접근해야 하는 경우 mutate를 다른 함수로 래핑해야 한다. 이는 React의 이벤트 pooling 때문이다.

```jsx
// This will not work in React 16 and earlier
const CreateTodo = () => {
  const mutation = useMutation({
    mutationFn: (event) => {
      event.preventDefault();
      return fetch("/api", new FormData(event.target));
    },
  });

  return <form onSubmit={mutation.mutate}>...</form>;
};

// This will work
const CreateTodo = () => {
  const mutation = useMutation({
    mutationFn: (formData) => {
      return fetch("/api", formData);
    },
  });
  const onSubmit = (event) => {
    event.preventDefault();
    mutation.mutate(new FormData(event.target));
  };

  return <form onSubmit={onSubmit}>...</form>;
};
```

## Resetting Mutation State : mutation 상태 재설정

mutation 요청의 오류나 데이터를 삭제해야 하는 경우가 있다. 이렇게 하면 재설정 기능을 사용하여 이를 처리할 수 있다.

```tsx
const CreateTodo = () => {
  const [title, setTitle] = useState("");
  const mutation = useMutation({ mutationFn: createTodo });

  const onCreateTodo = (e) => {
    e.preventDefault();
    mutation.mutate({ title });
  };

  return (
    <form onSubmit={onCreateTodo}>
      {mutation.error && (
        <h5 onClick={() => mutation.reset()}>{mutation.error}</h5>
      )}
      <input
        type="text"
        value={title}
        onChange={(e) => setTitle(e.target.value)}
      />
      <br />
      <button type="submit">Create Todo</button>
    </form>
  );
};
```

## Mutation Side Effects

useMutation 훅에는 mutation 수명 주기 중 모든 단계에서 빠르고 쉽게 side effects를 허용하는 몇가지 옵션이 함께 제공된다. 이는 mutation 및 optimistic 업데이트 이후 쿼리를 무효화하고 다시 가져오는데 유용하다.

```tsx
useMutation({
  mutationFn: addTodo,
  onMutate: (variables) => {
    // mutation이 발생할 예정이다!

    // 예를들어 롤백할 때 사용할 데이터가 포함된 컨텍스트를 선택적으로 반환한다.
    return { id: 1 };
  },
  onError: (error, variables, context) => {
    // An error happened!
    console.log(`rolling back optimistic update with id ${context.id}`);
  },
  onSuccess: (data, variables, context) => {
    // Boom baby!
  },
  onSettled: (data, error, variables, context) => {
    // Error or success... doesn't matter!
  },
});
```

콜백 함수에서 Promise를 반환할 때 다음 콜백이 호출되기 전에 먼저 대기하게 된다.

```tsx
useMutation({
  mutationFn: addTodo,
  onSuccess: async () => {
    console.log("I'm first!");
  },
  onSettled: async () => {
    console.log("I'm second!");
  },
});
```

mutate를 호출할 때 useMutation에 정의된 콜백 외에 추가 콜백을 트리거하고 싶을 수도 있다. 이는 component별 side effects를 트리거하는데 사용될 수 있다. 이를 위해 mutation 변수 다음에 mutate 함수에 동일한 콜백 옵션을 제공할 수 있다. 지원되는 옵션에는 onSuccess, onError, onSettled가 있다. mutation이 완료되기 전에 component가 언마운트되면 추가 콜백이 실행되지 않는다는 점을 명심할 것.

```tsx
useMutation({
  mutationFn: addTodo,
  onSuccess: (data, variables, context) => {
    // I will fire first
  },
  onError: (error, variables, context) => {
    // I will fire first
  },
  onSettled: (data, error, variables, context) => {
    // I will fire first
  },
});

mutate(todo, {
  onSuccess: (data, variables, context) => {
    // I will fire second!
  },
  onError: (error, variables, context) => {
    // I will fire second!
  },
  onSettled: (data, error, variables, context) => {
    // I will fire second!
  },
});
```

## Consecutive mutations 연속적인 mutation

연속적인 mutation의 경우 onSuccess, onError 및 onSettled 콜백을 처리하는데 약간의 차이가 있다. mutate 함수에 전달되면 component가 아직 마운트되어 있는 경우에만 한 번만 실행된다. 이는 mutate 함수가 호출될 때마다 mutation 관찰자가 제거되고 다시 구독된다는 사실 때문이다. 반대로 useMutation 핸들러는 각 mutate 호출에 대해 실행된다.

\*\* useMutation에 전달된 mutationFn은 비동기식일 가능성이 높다. 이 경우 mutation이 수행되는 순서는 mutate 함수 호출 순서와 다를 수 있다.

```tsx
useMutation({
  mutationFn: addTodo,
  onSuccess: (data, error, variables, context) => {
    // Will be called 3 times
  },
});

const todos = ["Todo 1", "Todo 2", "Todo 3"];
todos.forEach((todo) => {
  mutate(todo, {
    onSuccess: (data, error, variables, context) => {
      // Will execute only once, for the last mutation (Todo 3),
      // regardless which mutation resolves first
    },
  });
});
```

## Promise

성공 시 해결되거나 오류가 발생하는 Promise를 얻으려면 mutate 대신 mutateAsync를 사용하라.

```tsx
const mutation = useMutation({ mutationFn: addTodo });

try {
  const todo = await mutation.mutateAsync(todo);
  console.log(todo);
} catch (error) {
  console.error(error);
} finally {
  console.log("done");
}
```

## Retry 재시도

기본적으로 TanStack 쿼리는 오류 발생 시 mutation을 재시도하지 않지만 retry 옵션을 사용하면 가능하다.

```tsx
const mutation = useMutation({
  mutationFn: addTodo,
  retry: 3,
});
```

장치가 오프라인이여서 mutation이 실패하는 경우 장치가 다시 연결될 때 동일한 순서로 재시도된다.

## Persist mutations 지속

필요한 경우 mutation을 스토리지에 유지하고 나중에 재개할 수 있다. 이는 hydration 기능을 사용하여 수행할 수 있다.

```tsx
const queryClient = new QueryClient();

// Define the "addTodo" mutation
queryClient.setMutationDefaults(["addTodo"], {
  mutationFn: addTodo,
  onMutate: async (variables) => {
    // Cancel current queries for the todos list
    await queryClient.cancelQueries({ queryKey: ["todos"] });

    // Create optimistic todo
    const optimisticTodo = { id: uuid(), title: variables.title };

    // Add optimistic todo to todos list
    queryClient.setQueryData(["todos"], (old) => [...old, optimisticTodo]);

    // Return context with the optimistic todo
    return { optimisticTodo };
  },
  onSuccess: (result, variables, context) => {
    // Replace optimistic todo in the todos list with the result
    queryClient.setQueryData(["todos"], (old) =>
      old.map((todo) => (todo.id === context.optimisticTodo.id ? result : todo))
    );
  },
  onError: (error, variables, context) => {
    // Remove optimistic todo from the todos list
    queryClient.setQueryData(["todos"], (old) =>
      old.filter((todo) => todo.id !== context.optimisticTodo.id)
    );
  },
  retry: 3,
});

// Start mutation in some component:
const mutation = useMutation({ mutationKey: ["addTodo"] });
mutation.mutate({ title: "title" });

// If the mutation has been paused because the device is for example offline,
// Then the paused mutation can be dehydrated when the application quits:
const state = dehydrate(queryClient);

// The mutation can then be hydrated again when the application is started:
hydrate(queryClient, state);

// Resume the paused mutations:
queryClient.resumePausedMutations();
```

## Persisting Offline mutations 지속적인 오프라인 mutations

persistQueryClient 플러그인을 사용하여 오프라인 mutation을 지속하는 경우 기본 mutation 기능을 제공하지 않으면 페이지를 다시 로드할 때 mutation 을 재개할 수 없다.

이는 기술적 한계이다. 외부 스토리지에 지속되면 함수를 직렬화할 수 없으므로 mutation 상태만 지속된다. hydration 이후에는 mutation을 트리거하는 component가 마운트되지 않을 수 있으므로, `resumePausedMutations`를 호출하면 No mutationFn 을 찾을 수 없다는 오류가 발생할 수 있다.

```tsx
const persister = createSyncStoragePersister({
  storage: window.localStorage,
});
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      gcTime: 1000 * 60 * 60 * 24, // 24 hours
    },
  },
});

// we need a default mutation function so that paused mutations can resume after a page reload
queryClient.setMutationDefaults(["todos"], {
  mutationFn: ({ id, data }) => {
    return api.updateTodo(id, data);
  },
});

export default function App() {
  return (
    <PersistQueryClientProvider
      client={queryClient}
      persistOptions={{ persister }}
      onSuccess={() => {
        // resume mutations after initial restore from localStorage was successful
        queryClient.resumePausedMutations();
      }}
    >
      <RestOfTheApp />
    </PersistQueryClientProvider>
  );
}
```
