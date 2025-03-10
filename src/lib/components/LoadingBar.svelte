<script lang="ts">
	type Props = {
		value?: number;
		class?: string;
	};

	let { value, class: className }: Props = $props();

	let displayValue = $state(0);

	$effect(() => {
		if (!value) displayValue = 0;
		else if (value <= 0) displayValue = 0;
		else if (value > 100) displayValue = 100;
		else displayValue = value;
	});
</script>

<div
	class={`animate__animated animate__fadeIn h-2 rounded-2xl border border-stone-400 bg-white ${className ? className : ''}`}
>
	<div class="h-full bg-slate-600" style={`width: ${displayValue}%`}></div>
</div>
