# Svelte LifeCycle

    lifecycle 즉 생명주기를 설명하도록 하겠습니다.

    라이프사이클 함수는 컴포넌트가 화면에 마운트 되기 전 되고 난 후 등의 상태에 어떤 작업을 처리할 때 사용하는 함수라고 보시면 됩니다.
    onMount: 컴포넌트가 돔에 마운트 되면 실행
    onDestroy: 컴포넌트가 해제된 후 실행
    beforeupdate: 컴포넌트가 마운트 되기전 실행
    afterUpdate: 컴포넌트가 마운드 된 후 실행
    tick: 컴포넌트 변경이 완료되면 실행(라이프사이클과 조금 성격이 다름)
    
    svlete 라이프사이클의 전체적인 실행 순서를 보면
    beforeUpdate → onMount → afterUpdate → onDestroy
    
    그리고 부모와 자식 컴포넌트 사이에 라이프사이클의 경우 는 부모의 beforeUpdate 이후 자식 컴포넌트의 모든 라이프사이클 즉 afterUpdate 이후에 부모의 onMount 가 발생합니다. 예제는 이전에 만든 todo 앱에 라이프사이클 소스를 넣어 실행해 보도록 하겠습니다.

app.svelte
```js
import { onMount, onDestroy, beforeUpdate, afterUpdate, tick } from 'svelte'

...

onMount(async() => {
	console.log('App onMount')
})

onDestroy(async() => {
	console.log('App onDestroy')
})

beforeUpdate(async() => {
  console.log('App beforeUpdate')
})

afterUpdate(async() => {
	console.log('App afterUpdate')
})
```
child.svelte
```js
import { onMount, onDestroy, beforeUpdate, afterUpdate, tick } from 'svelte'

...

onMount(async() => {
	console.log('child onMount')
})

onDestroy(async() => {
	console.log('child onDestroy')
})

beforeUpdate(async() => {
	console.log('child beforeUpdate')
})

afterUpdate(async() => {
	console.log('child afterUpdate')
})
```
결과

    App beforeUpdate // 부모컴포넌트
    TodoList.svelte:20 TodoLists beforeUpdate // 자식컴포넌트
    TodoList.svelte:12 TodoLists onMount // 자식컴포넌트
    TodoList.svelte:27 TodoLists afterUpdate // 자식컴포넌트
    App.svelte:103 App onMount // 부모컴포넌트
    App.svelte:115 App afterUpdate // 부모컴포넌트

    tick의 경우는 조금 다른 성격을 가집니다. 컴포넌트가 업데이트 된 후 
    promise객체를 반환하는 형태인데요. 예제를 통해서 이부분도 알아보도록 하겠습니다

```js
import { onMount, onDestroy, beforeUpdate, afterUpdate, tick } from 'svelte'

...

onMount(async() => {
	console.log('child onMount')
})

onDestroy(async() => {
	console.log('child onDestroy')
})

beforeUpdate(async() => {
  console.log('the component is about to update');
  await tick()
  console.log('the component just updated');  
})

afterUpdate(async() => {
	console.log('child afterUpdate')
})
```

위 소스에서 await tick()을 넣고 넣지 않고의 console.log 위치를 보시겠습니다.

await tick()이 없을 경우

    App beforeUpdate
    TodoList.svelte:21 the component is about to update
    TodoList.svelte:23 the component just updated
    TodoList.svelte:12 TodoLists onMount
    TodoList.svelte:27 TodoLists afterUpdate
    App.svelte:103 App onMount
    App.svelte:115 App afterUpdate

await tick()이 있을 경우

    App beforeUpdate
    TodoList.svelte:21 the component is about to update
    TodoList.svelte:12 TodoLists onMount
    TodoList.svelte:27 TodoLists afterUpdate
    App.svelte:103 App onMount
    App.svelte:115 App afterUpdate
    TodoList.svelte:23 the component just updated
    
tick() 함수를 await과 함께 넣을 경우는 모든 DOM이 랜더링 되고 난 후에 이를 기다렸다가 the component just updated 메시지가 나온 것을 볼 수 있습니다.

즉 tick()함수를 이용하면 라이프사이클함수의 상태와는 별개로 모든 돔의 렌더링을 기다리고 그후에 작업을 할 수 있는 작업을 할 수 있습니다.
