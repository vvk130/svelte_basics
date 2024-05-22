Statements

<script>
	let count = 0;
	$: alert(`the count is ${count}`)
	function handleClick() {
		count += 1;
	}
</script>

<button on:click={handleClick}>
	Clicked {count}
	{count === 1 ? 'time' : 'times'}
</button>
<p></p>

Updating arrays and objects 

<script>
	let numbers = [1, 2, 3, 4];

	function addNumber() {
		numbers = [...numbers, numbers.length +1];
	}

	$: sum = numbers.reduce((total, currentNumber) => total + currentNumber, 0);
</script>

<p>{numbers.join(' + ')} = {sum}</p>

<button on:click={addNumber}>
	Add a number
</button>

Declaring props

<script>
    import Nested from './Nested.svelte';
   </script>
   
   <Nested answer={42} />

<script>
	export let answer;
</script>

<p>The answer is {answer}</p>
   
Default values

<script>
	export let answer = 'a default answer';
</script>

<p>The answer is {answer}</p>

<script>
	import Nested from './Nested.svelte';
</script>

<Nested answer={42} />
<Nested />

Spread props
since the properties of pkg correspond to the component's expected props, we can 'spread' them onto the component instead:

<script>
	import PackageInfo from './PackageInfo.svelte';

	const pkg = {
		name: 'svelte',
		speed: 'blazing',
		version: 4,
		website: 'https://svelte.dev'
	};
</script>

<PackageInfo {...pkg} />

<script>
	export let name;
	export let version;
	export let speed;
	export let website;

	$: href = `https://www.npmjs.com/package/${name}`;
</script>

<p>
	The <code>{name}</code> package is {speed} fast. Download version {version} from
	<a {href}>npm</a> and <a href={website}>learn more here</a>
</p>

Each block

<script>
	const colors = ['red', 'orange', 'yellow', 'green', 'blue', 'indigo', 'violet'];
	let selected = colors[0];
</script>

<h1 style="color: {selected}">Pick a colour</h1>

<div>
	{#each colors as color, i}
		<button
			aria-current={selected === color}
			aria-label={color}
			style="background: {color}"
			on:click={() => selected = color}
		>{i + 1}</button>
	{/each}
</div>

Keyed each blocks

{#each things as thing (thing.id)}
	<Thing name={thing.name} />
{/each}

Await blocks

{#await promise}
<p>...waiting</p>
{:then number}
<p>{number}</p>
{:catch error}
	<p>{error.message}</p>
{/await}

DOM Events

<div on:pointermove={handleMove}>
	The pointer is at {m.x} x {m.y}
</div>

Inline handlers

<div
	on:pointermove={(e) => {
		m = { x: e.clientX, y: e.clientY };
	}}
>

Event modifiers

<button on:click|once={() => alert('clicked')}>
	Click me
</button>

The full list of modifiers:

    preventDefault — calls event.preventDefault() before running the handler. Useful for client-side form handling, for example.
    stopPropagation — calls event.stopPropagation(), preventing the event reaching the next element
    passive — improves scrolling performance on touch/wheel events (Svelte will add it automatically where it's safe to do so)
    nonpassive — explicitly set passive: false
    capture — fires the handler during the capture phase instead of the bubbling phase
    once — remove the handler after the first time it runs
    self — only trigger handler if event.target is the element itself
    trusted — only trigger handler if event.isTrusted is true, meaning the event was triggered by a user action rather than because some JavaScript called element.dispatchEvent(...)

You can chain modifiers together, e.g. on:click|once|capture={...}.

Component events

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

<script>
	import Inner from './Inner.svelte';

	function handleMessage(event) {
		alert(event.detail.text);
	}
</script>

<Inner on:message={handleMessage} />

Event forwarding

<script>
	import Outer from './Outer.svelte';

	function handleMessage(event) {
		alert(event.detail.text);
	}
</script>

<Outer on:message={handleMessage} />

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

<script>
	import Inner from './Inner.svelte';
</script>

<Inner on:message />

DOM event forwarding
 
App.svelte
<script>
	import BigRedButton from './BigRedButton.svelte';
	import horn from './horn.mp3';

	const audio = new Audio();
	audio.src = horn;

	function handleClick() {
		audio.load();
		audio.play();
	}
</script>

<BigRedButton on:click={handleClick} />

BigRedButton.svelte
<button on:click>
	Push
</button>

Text input

<script>
	let name = 'world';
</script>

<input bind:value={name} />

<h1>Hello {name}!</h1>

Checked input

<input type="checkbox" bind:checked={yes} />

Group inputs 

{#each ['cookies and cream', 'mint choc chip', 'raspberry ripple'] as flavour}
	<label>
		<input
			type="checkbox"
			name="flavours"
			value={flavour}
			bind:group={flavours}
		/>

		{flavour}
	</label>
{/each}

onMount

<script>
	import { onMount } from 'svelte';
	import { paint } from './gradient.js';

	onMount(() => {
		const canvas = document.querySelector('canvas');
		const context = canvas.getContext('2d');

		let frame = requestAnimationFrame(function loop(t) {
			frame = requestAnimationFrame(loop);
			paint(context, t);
		});

		return () => {
			cancelAnimationFrame(frame);
		};
	});
</script>

beforeUpdate and afterUpdate

<script>
	import Eliza from 'elizabot';
	import {
		beforeUpdate,
		afterUpdate
	} from 'svelte';

	let div;
	let autoscroll = false;

	beforeUpdate(() => {
		if (div) {
			const scrollableDistance = div.scrollHeight - div.offsetHeight;
			autoscroll = div.scrollTop > scrollableDistance - 20;
		}
	});

	afterUpdate(() => {
		if (autoscroll) {
			div.scrollTo(0, div.scrollHeight);
		}
	});

    tick

    <script>
	import { tick } from 'svelte';

	let text = `Select some text and hit the tab key to toggle uppercase`;

	async function handleKeydown(event) {
		if (event.key !== 'Tab') return;

		event.preventDefault();

		const { selectionStart, selectionEnd, value } = this;
		const selection = value.slice(selectionStart, selectionEnd);

		const replacement = /[a-z]/.test(selection)
			? selection.toUpperCase()
			: selection.toLowerCase();

		text =
			value.slice(0, selectionStart) +
			replacement +
			value.slice(selectionEnd);

		await tick();
		this.selectionStart = selectionStart;
		this.selectionEnd = selectionEnd;
	}
</script>

<textarea
	value={text}
	on:keydown={handleKeydown}
/>

Writable store
.update(), set() operations

<script>
	import { count } from './stores.js';

	function increment() {
		count.update((n) => n + 1);
	}
</script>

<button on:click={increment}>
	+
</button>

Autosubscribtions

-Help to avoid boilerplate code, $ before
-NOTE: Auto-subscription only works with store variables that are declared (or imported) at the top-level scope of a component.
-NOTE: Any name beginning with $ is assumed to refer to a store value. It's effectively a reserved character — Svelte will prevent you from declaring your own variables with a $ prefix.

<script>
	import { count } from './stores.js';
	import Incrementer from './Incrementer.svelte';
	import Decrementer from './Decrementer.svelte';
	import Resetter from './Resetter.svelte';
</script>

<h1>The count is {$count}</h1>

<Incrementer />
<Decrementer />
<Resetter />

Readable stores

import { readable } from 'svelte/store';

export const time = readable(new Date(), function start(set) {
	const interval = setInterval(() => {
		set(new Date());
	}, 1000);

	return function stop() {
		clearInterval(interval);
	};
});

<script>
	import { time } from './stores.js';

	const formatter = new Intl.DateTimeFormat(
		'en',
		{
			hour12: true,
			hour: 'numeric',
			minute: '2-digit',
			second: '2-digit'
		}
	);
</script>

<h1>The time is {formatter.format($time)}</h1>

Derived stores

import { readable, derived } from 'svelte/store';

export const time = readable(new Date(), function start(set) {
	const interval = setInterval(() => {
		set(new Date());
	}, 1000);

	return function stop() {
		clearInterval(interval);
	};
});

const start = new Date();

export const elapsed = derived(
	time,
	($time) => Math.round(($time - start) / 1000)
);

Custom Stores also exist

Store bindings

<script>
	import { name, greeting } from './stores.js';
</script>

<h1>{$greeting}</h1>
<input bind:value={$name} />

<button on:click={() => $name += '!'}>
	Add exclamation mark!
</button>

store.js
import { writable, derived } from 'svelte/store';

export const name = writable('world');

export const greeting = derived(name, ($name) => `Hello ${$name}!`);
