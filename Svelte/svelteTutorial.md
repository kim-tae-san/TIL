# Svelte Tutorial

    중괄호로 변수참조가능

    Svelte는 image 태그의 속성이랑 값이 비슷할 경우 
```js
<img src={src} alt="A man dances.">
>  <img {src} alt="A man dances.">
```
    이렇게 사용할 수 있도록 하였다.

    다른 svelte파일을 import할수도 있다. 사용법은 아래와 같음.

```js
<script>
	import Nested from './Nested.svelte';
</script>
<p>This is a paragraph.</p>
<Nested/>

<style>
	p {
		color: purple;
		font-family: 'Comic Sans MS', cursive;
		font-size: 2em;
	}
</style>
```

> @html 태그

    일반적으로 <나 >는 char로 특별한 의미는 없다. 그런데 컴포넌트에서 문자로 취급되는 <>를 렌더링 하고 싶을때 @html태그를 사용한다
```html
ex) <p>{@html string}</p>
```

    * 주의점으로 svelte에서는 @html 태그에 들어오는 요청을 확인하지 않기 때문에 신뢰하지않는 외부 사용자로부터의 XSS공격에 취약할 수 있다.


> $: reactive value

```js
<script>
	let count = 0;
	$: doubled = count * 2;

	function handleClick() {
		count += 1;
	}
</script>

<button on:click={handleClick}>
	Clicked {count} {count === 1 ? 'time' : 'times'}
</button>

<p>
	{count} doubled is {doubled}
</p>

```

    로그로도 이렇게 쓸 수 있다.
```js
    $: if (count >= 10) {
	alert('count is dangerously high!');
	count = 9;
    }
```

    Svelte의 reactivity는 assignment에 의해 발동된다. 메소드는 어레이나 오브젝트 스스로의 변경으로는 트리거 되지 않는다. 간접참조도 트리거 되지 않는다.


> export 

    export 선언을 이용해 변수가 다른 모듈에서 참조될 수 있게한다.
    

> 조건문!

    html에서는 조건문을 제공하지 않는데 svelte에서는 제공한다!!

```js
<script>
	let user = { loggedIn: false };

	function toggle() {
		user.loggedIn = !user.loggedIn;
	}
</script>

{#if user.loggedIn}
	<button on:click={toggle}>
		Log out
	</button>
{/if}

{#if !user.loggedIn}
	<button on:click={toggle}>
		Log in
	</button>
{/if}
```

    위는 예시다. #은 block opening, :는 block continuation, / 는 block closing을 의미한다.

> 반복문 each

```js
<script>
	let cats = [
		{ id: 'J---aiyznGQ', name: 'Keyboard Cat' },
		{ id: 'z_AbfPXTKms', name: 'Maru' },
		{ id: 'OUtn3pvWmpg', name: 'Henri The Existential Cat' }
	];
</script>

<h1>The Famous Cats of YouTube</h1>

<ul>
	<!-- open each block -->
	{#each cats as cat}
		<li><a target="_blank" href="https://www.youtube.com/watch?v={cat.id}">
			{cat.name}
		</a></li>
	{/each}
	<!-- close each block -->
</ul>
```

> await 

    svelte에서는 비동기 함수를 위한 await 키워드를 제공한다 

    예시는 아래와 같다.
```js
<script>
	async function getRandomNumber() {
		const res = await fetch(`/tutorial/random-number`);
		const text = await res.text();

		if (res.ok) {
			return text;
		} else {
			throw new Error(text);
		}
	}
	
	let promise = getRandomNumber();

	function handleClick() {
		promise = getRandomNumber();
	}
</script>

<button on:click={handleClick}>
	generate random number
</button>

{#await promise}
	<p>...waiting</p>
{:then number}
	<p>The number is {number}</p>
{:catch error}
	<p style="color: red">{error.message}</p>
{/await}

```

> inline handler

```js
<script>
	let m = { x: 0, y: 0 };
</script>

<div on:mousemove="{e => m = { x: e.clientX, y: e.clientY }}">
	The mouse position is {m.x} x {m.y}
</div>

<style>
	div { width: 100%; height: 100%; }
</style>
```

> event modifiers

    The full list of modifiers:

    preventDefault — calls event.preventDefault() before running the handler. Useful for client-side form handling, for example.
    stopPropagation — calls event.stopPropagation(), preventing the event reaching the next element
    passive — improves scrolling performance on touch/wheel events (Svelte will add it automatically where it's safe to do so)
    nonpassive — explicitly set passive: false
    capture — fires the handler during the capture phase instead of the bubbling phase (MDN docs)
    once — remove the handler after the first time it runs
    self — only trigger handler if event.target is the element itself
    trusted — only trigger handler if event.isTrusted is true. I.e. if the event is triggered by a user action.

```js
<script>
	function handleClick() {
		alert('no more alerts')
	}
</script>

<button on:click|once={handleClick}>
	Click me
</button>
```

> component events

    다른 컴포넌트의 이벤트를 디스패치 할 수 있는 예제이다. dispatch에 이름을 기준으로 listen한다. 

```js
Main
<script>
	import Inner from './Inner.svelte';

	function handleMessage(event) {
		alert(event.detail.text);
	}
</script>

<Inner on:message={handleMessage}/>

Inner
<script>
	import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher();

	function sayHello() {
		dispatch('messe', {
			text: 'Hello!'
		});
	}
</script>

<button on:click={sayHello}>
	Click to say hello
</button>

```

> event forwarding

```js
App

<script>
	import Outer from './Outer.svelte';

	function handleMessage(event) {
		alert(event.detail.text);
	}
</script>

<Outer on:message={handleMessage}/>

inner 

<script>
	import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher();

	function sayHello() {
		dispatch('message', {
			text: 'Hello!'
		});
	}
</script>

<button on:click={sayHello}>
	Click to say hello
</button>

outer

<script>
	import Inner from './Inner.svelte';
</script>

<Inner on:message/>
```