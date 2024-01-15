[👉🏻 공식문서 바로가기](https://tanstack.com/query/v5/docs/react/overview)

## Overview 개요

TanStack Query(전, 리엑트쿼리)는 종종 웹어플리케이션의 missing (누락된) 데이터 패칭을 위한 라이브러리로 설명되지만, 보다 기술적인 용어로는 웹 어플리케이션에서 서버의 상태 fetching, 캐싱, 동기화 및 서버 상테 업데이트를 손쉽게 만들어주는 도구 이다.

## Motivation 동기

대부분의 core 웹 프레임워크는 데이터를 획일적으로 가져오거나 업데이트하는 방법에 대한 명확한 의견을 제시하지 않는다. 이로인해 개발자들은 종종 data-fetching에 대한 strict opinions를 캡슐화하는 메타 프레임워크를 구축하거나, 자체 data fetching 하는것을 발명하게 된다. 이는 주로 컴포넌트 기반 상태 및 side-effects를 묶어 사용하거나 더 일반적인 목적의 상태관리 라이브러리를 사용하여 앱 전반에 걸쳐 비동기 데이터를 저장하고 제공하는 것을 의미한다.

대부분의 전통적인 상태관리 라이브러리들은 클라이언트 상태를 작업하는데에는 훌륭하지만, 비동기 또는 서버 상태와 작업하는데는 그리 효과적이지 않다. 이는 서버 상태가 완전히 다르기 때문이다. 일단, 서버 상태는 다음과 같다.

- 원격에서 당신의 통제 불가능한 곳에서 지속적으로 저장된다.
- 패칭 및 업데이트에 비동기 API가 필요하다.
- 다른 사람들과의 ownership 공유를 의미하며, 당신이 인지하지 못한 상태에서 다른사람에 의해 변경될 수 있다.
- 조심하지 않으면 애플리케이션에 "out of data" 상태가 될 수 있다.

한번 애플리케이션 내에서 서버 상태의 nature을 이해하면, 더많은 challenges가 발생할 것이다. 예를들면

- 캐싱 (아마도 프로그래밍에서 제일 어려운 것 중 하나)
- 동일한 데이터에 대한 여러 요청을 단일 요청으로 중복 제거
- background에서 "out of data" 업데이트
- 데이터가 "out of data" 인지 알아내기
- 데이터 업데이트를 가능한 빠르게 반영하기
- 페이지네이션 및 데이터의 lazy 로딩과 같은 성능 최적화
- 서버 상태의 메모리 관리 및 carbage colletion
- 구조 공유를 통한 쿼리 결과의 메모이제이션

만약, 당신이 리스트에 압도되지 않았다면, 아마도 이미 모든 서버 상태 문제를 해결했거나 상을 받을만한 일을 해냈을 것이다. 그러나 만약 여러분이 대다수와 같다면, 여전히 이러한 문제 중 일부 또는 대부분에 대처하지 않은 것일 뿐이며, 겉핥기에 불과할 뿐이다.

React Query는 확실히 서버 상태를 관리하는데 최고의 라이브러리 중 하나이다. zero-config로 놀랄만큼 잘 작동하며, 앱이 성장함에 따라 원하는대로 커스터마이징할 수 있다.

React query를 사용하면 서버 상태의 까다로운 challenges과 장애를 극복할 수 있으며, 앱 데이터가 여러분을 통제하기 전에 여러분이 그것을 통제할 수 있게 해준다.

좀더 기술적인 측면에서, React Query는

- 애플리케이션에서 복잡하고 misunderstood(오해많은) 코드 라인을 제거하고, 그것을 몇줄의 React Query 로직으로 대체하는 데 도움이 될 것이다.
- 애플리케이션을 더 유지보수하기 쉽고 새로운 기능을 쉽게 구축할 수 있도록 도와줄 것이다. 새로운 서버 상태 데이터 소스를 연결하는데 걱정할 필요가 없다.
- 당신의 애플리케이션이 이전보다 더 빠르고 반응성이 뛰어나게 느껴지도록 함으로써 최종 사용자에게 직접적인 영향을 미칠 것이다.
- 잠재성 그리고 메모리 성능을 향상시키는데 도움이 될 것이다.

## 말 그만하고, 코드 보여줄게!

아래 예시에서, React Query를 가장 기본적이고 간단한 형태로 사용하여

```javaScript
import {
	QueryClient,
	QueryClientProvider,
	useQuery
} from '@tanstack/react-query'

const queryClient = new QueryClient()

export default function App() {
	return (
		<QueryClientProvider client={queryClient}>
      <Example />
    </QueryClientProvider>
	)
}

function Example() {
  const { isPending, error, data } = useQuery({
    queryKey: ['repoData'],
    queryFn: () =>
      fetch('https://api.github.com/repos/TanStack/query').then((res) =>
        res.json(),
      ),
  })

  if (isPending) return 'Loading...'

  if (error) return 'An error has occurred: ' + error.message

  return (
    <div>
      <h1>{data.name}</h1>
      <p>{data.description}</p>
      <strong>👀 {data.subscribers_count}</strong>{' '}
      <strong>✨ {data.stargazers_count}</strong>{' '}
      <strong>🍴 {data.forks_count}</strong>
    </div>
  )
}


```
