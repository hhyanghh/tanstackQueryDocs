[👉🏻 공식문서 바로가기](https://tanstack.com/query/v5/docs/react/guides/important-defaults)

## Important Defaults 중요한 default 셋팅

기본적으로, tanstack query는 공격적이지만 sane한 default들로 구성되어있다.
때때로, 이러한 기본 설정은 새로운 사용자들을 당황하게 할 수 있으며, 사용자가 이를 알지 못하는 경우 학습 및 디버깅을 어렵게 할 수 있습니다.
Tanstack query를 배우고 사용하면서 이러한 기본 설정을 기억해 두세요. :

- 기본적으로 ‘useQuery‘ 또는 ’useInfiniteQuery’를 통한 쿼리 인스턴스는 캐시된 데이터를 stale로 간주한다.

> 이 동작을 변경하려면 ‘staleTime’ 옵션을 사용하여 전역적, 쿼리별로 쿼리를 구성할 수 있다. 더 긴 staleTime은 쿼리가 데이터를 자주 다시 리패치하지 않음을 의미한다.

- Stale Queries 백그라운드에서 자동적으로 다음과 같을때 리패치된다. :

  - 새로운 쿼리 인스턴스가 마운트 되었을때
  - 윈도우가 refocused 되었을때
  - 네트워크가 다시 연결되었을때
  - the query is optionally configyred with a refetch interval

> 이 functionality 를 변경하려면, 당신은 옵션을 사용할 수 있다. refetchOnMount, refetchOnWindowFocus, refetchOnReconnect, refetchInterval

- ‘useQuery’, ‘useInfiniteQuery’ 또는 쿼리 observer의 active instance가 더 이상 아닌 쿼리 결과는 ‘inactive’으로 표시되고 나중에 다시 사용될 경우를 대비해 캐시에 남아있는다.

- 기본적으로, ‘inactive’ 쿼리는 5분 후에 가비지 콜렉티드 된다. (지워진다.)

> 이를 변경하려면, 쿼리의 default ‘gcTime’을 ‘1000 _ 60 _ 5’초 보다 더 자주로 변경하면 된다.

- 실패한 쿼리는 3번 자동으로 retry 되며, 오류를 캡쳐하여 UI에 표시하기 전에 exponetial backoff 지연이 발생한다.

> 이를 변경하려면, default ‘retry’와 ‘retryDelay’ 옵션을 3보다 자주로 또는 default eponential backoff function 으로 변경하면 된다.

- 기본적으로 쿼리 results는 데이터가 실제로 변경되었는지 감지하기 위해 구조적으로 공유되며, 그렇지 않은 경우 데이터 참조는 변경되지 않은 상태로 유지되어 useMemo 및 useCallback과 관련된 value stabilization에 더 도움이 된다. 만약 이 컨셉이 foreign으로 들린다면, 그러면 걱정하지 말라!
  99.9% 의 경우 이 기능을 비활성화 할 필요가 없으며 무료로 앱 성능을 향상시킬 수 있다.

> Structural 공유는 오직 JSON 호환 값에만 적용되며, 다른 유형의 값들은 항상 변경된 것으로 간주된다. 예를들어 large response 으로 인한 성능 이슈가 발생하는 경우, config.structuralSharing 플래그를 사용하여 이 기능을 비활성화 할 수 있다. 쿼리 response에서 JSON 호환 값이 아닌 값과 처리하고 여전히 데이터가 변경되었는지 여부를 감지하려면, config.structuralSharing으로 custom 함수를 제공하여 이전 및 새 응답에서 값을 계산할 수 있다.

_c.f. 전체 페이지 캡쳐화면_
![이미지2](/img/day3.png)
