<script lang="ts">
	import "../app.css";
	import { Toaster } from "$lib/components/ui/sonner/index.js";
	import { ModeWatcher } from "mode-watcher";
	import Header from "$lib/components/page/Header.svelte";
    import Footer from "$lib/components/page/Footer.svelte";

	let { children } = $props();
</script>

<ModeWatcher />

<div
	class="min-h-screen bg-gradient-to-br from-slate-50 via-blue-50 to-indigo-50 relative overflow-hidden"
>
	<!-- Background decorative elements -->
	<div
		class="absolute inset-0 bg-grid-slate-100 [mask-image:linear-gradient(0deg,#fff,rgba(255,255,255,0.6))] pointer-events-none"
	></div>
	<div
		class="absolute top-0 right-0 -translate-y-12 translate-x-12 opacity-20"
	>
		<div
			class="w-96 h-96 rounded-full bg-gradient-to-br from-blue-400 to-purple-600"
		></div>
	</div>
	<div
		class="absolute bottom-0 left-0 translate-y-12 -translate-x-12 opacity-20"
	>
		<div
			class="w-80 h-80 rounded-full bg-gradient-to-br from-cyan-400 to-blue-600"
		></div>
	</div>
	<div class="relative max-w-7xl mx-auto px-4 py-8 space-y-8">
		<Header />
		{@render children()}
		<Footer/>
	</div>
</div>
<Toaster />
