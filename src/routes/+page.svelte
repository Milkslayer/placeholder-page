<script lang="ts">
	import LoadingBar from '$lib/components/LoadingBar.svelte';
	import { onMount } from 'svelte';

	const h1Text = 'Take a break,';
	const h2Text = 'enjoy the view.';
	const typingSpeed = 50;
	let p1: HTMLElement;
	let p2: HTMLElement;
	let imgCount = 31;

	const imageUrls = Array.from({ length: imgCount }, (_, i) => `/sequence/${i + 1}.png`);
	let loadedImages = new Array<HTMLImageElement>(imgCount);
	let totalImagesLoaded = 0;
	let loadingProgress = $state(0);
	let loadComplete = $state(false);
	let currentImageIndex = $state(0);
	let isPlaying = false;
	let fps = $state(4);
	let animationId: number;
	let hideText = $state(false);
	function sleep(ms: number) {
		return new Promise((resolve) => setTimeout(resolve, ms));
	}

	const fill = async (header: HTMLElement, text: string) => {
		for (let i = 0; i < text.length; i++) {
			header.innerHTML += text.charAt(i);
			await sleep(typingSpeed);
		}
	};

	const fillP1 = () => fill(p1, h1Text);
	const fillP2 = () => fill(p2, h2Text);

	const loadImage = async (url: string): Promise<HTMLImageElement> => {
		return new Promise((resolve, reject) => {
			const img = new Image();
			img.onload = () => resolve(img);
			img.onerror = () => reject(new Error(`Failed to load image: ${url}`));
			img.src = url;
		});
	};

	const loadImages = async () => {
		const maxConcurrent = 5; // Control how many images load at once
		loadedImages = new Array(imageUrls.length);

		// Create a pool of promises with controlled concurrency
		const loadImagesInBatches = async () => {
			for (let i = 0; i < imageUrls.length; i += maxConcurrent) {
				const batch = imageUrls.slice(i, i + maxConcurrent).map(async (url, batchIndex) => {
					const index = i + batchIndex;
					try {
						const img = await loadImage(url);
						loadedImages[index] = img;
						totalImagesLoaded++;
						loadingProgress = (totalImagesLoaded / imageUrls.length) * 100;
					} catch (error) {
						console.error(error);
						totalImagesLoaded++;
						loadingProgress = (totalImagesLoaded / imageUrls.length) * 100;
					}
				});

				// Wait for current batch to complete before loading next batch
				await Promise.all(batch);
			}

			loadComplete = true;
		};

		// Start the loading process in the background without awaiting it
		loadImagesInBatches();
	};

	function startPlayback() {
		if (!loadComplete || isPlaying) return;

		isPlaying = true;
		currentImageIndex = 0;
		const frameInterval = 1000 / fps;
		let lastTimestamp = 0;

		function animate(timestamp: number) {
			if (!isPlaying) return;

			const elapsed = timestamp - lastTimestamp;

			if (elapsed > frameInterval) {
				lastTimestamp = timestamp - (elapsed % frameInterval);
				currentImageIndex = (currentImageIndex + 1) % loadedImages.length;
			}

			animationId = requestAnimationFrame(animate);
		}

		animationId = requestAnimationFrame(animate);
	}

	$effect(() => {
		if (loadComplete) {
			startPlayback();
			setTimeout(() => {
				hideText = true;
				fps = 6;
			}, 8000);
		}
	});

	onMount(() => {
		loadImages();
		setTimeout(fillP1, 1500);
		setTimeout(fillP2, 2500);

		return () => {
			if (animationId) {
				cancelAnimationFrame(animationId);
			}
		};
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
		src={loadComplete ? loadedImages[currentImageIndex].src : 'img/small.jpg'}
		alt="bg"
		class="absolute top-0 left-0 -z-10 aspect-9/7 h-dvh w-auto object-cover md:left-1/2 md:-translate-x-1/2"
	/>

	<div class="flex flex-col gap-2 {hideText ? 'animate__animated animate__fadeOut' : ''}">
		<div
			class="ffont animate__animated animate__fadeInUp animate__slow rounded-lg bg-stone-200/60 p-8 text-center text-4xl"
		>
			<p bind:this={p1}></p>
			<p bind:this={p2}></p>
		</div>
		{#if !loadComplete}
			<LoadingBar class="animate__delay-4s" value={loadingProgress} />
		{/if}
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
