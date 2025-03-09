<script lang="ts">
	type Props = {
		value?: number;
		class?: string;
	};

	let { value, class: className }: Props = $props();

	const getValue = () => {
		if (!value) return 0;
		if (value < 0) return 0;
		if (value > 100) return 100;
		return value;
	};
</script>

<div
	class="animate__animatedd animate__fadeIn animate__delay-5s h-2 rounded-2xl border border-stone-400 bg-white"
>
	<div class={`h-full bg-slate-600 ${className ? className : ''}`} style={`width: ${value}%`}></div>
</div>
