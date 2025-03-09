<script lang="ts">
	import LoadingBar from '$lib/components/LoadingBar.svelte';
	import { onMount } from 'svelte';

	const h1Text = 'Take a break,';
	const h2Text = 'enjoy the view.';
	const typingSpeed = 50;
	let p1: HTMLElement;
	let p2: HTMLElement;
	let i = 0;

	function sleep(ms: number) {
		return new Promise((resolve) => setTimeout(resolve, ms));
	}

	const fill = async (header: HTMLElement, text: string) => {
		for (let i = 0; i < text.length; i++) {
			header.innerHTML += text.charAt(i);
			await sleep(50);
		}
	};

	const fillP1 = () => fill(p1, h1Text);
	const fillP2 = () => fill(p2, h2Text);

	onMount(() => {
		setTimeout(fillP1, 1800);
		setTimeout(fillP2, 2800);
	});
</script>

<div class="relative flex h-dvh flex-col items-center justify-center overflow-hidden">
	<div class="-z-30 hidden md:block">
		<img
			src="img/small.jpg"
			alt="blurred bg"
			class="absolute top-1/2 left-0 -z-20 aspect-9/7 w-full -translate-y-1/2"
		/>
		<div class="absolute top-0 left-0 h-full w-full blur backdrop-blur-lg"></div>
	</div>

	<img
		src="img/small.jpg"
		alt="bg"
		class="absolute top-0 left-0 -z-10 aspect-9/7 h-dvh w-auto object-cover md:left-1/2 md:-translate-x-1/2"
	/>

	<div class="flex flex-col gap-2">
		<div
			class="ffont animate__animated animate__fadeInUp animate__slow rounded-lg bg-stone-200/60 p-8 text-center text-4xl"
		>
			<p bind:this={p1}></p>
			<p bind:this={p2}></p>
		</div>
		<LoadingBar value={1} />
	</div>

	<div
		class="ffont absolute right-2 bottom-1 flex flex-row items-center justify-center rounded-lg bg-sky-700 px-1 py-0.5 text-2xl font-bold text-white italic"
	>
		<a class="text-base" href="https://www.linkedin.com/in/muhammed-bakijev/">MB</a>
	</div>
</div>

<style>
	.blur {
		backdrop-filter: blur(16px);
	}
</style>
