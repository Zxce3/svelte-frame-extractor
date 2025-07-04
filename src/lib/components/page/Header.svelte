<script>
    import VideoIcon from "@lucide/svelte/icons/video";
    import CheckCircleIcon from "@lucide/svelte/icons/check-circle";
</script>

<!-- Enhanced Header -->
<div class="text-center space-y-6">
    <div
        class="inline-flex items-center justify-center p-3 bg-gradient-to-br from-blue-500 to-purple-600 rounded-2xl shadow-lg mb-4"
    >
        <VideoIcon class="w-8 h-8 text-white" />
    </div>
    <div class="space-y-3">
        <h1
            class="text-5xl font-bold bg-gradient-to-r from-slate-900 via-purple-900 to-slate-900 bg-clip-text text-transparent"
        >
            Video Frame Extractor
        </h1>
        <p class="text-xl text-slate-600 max-w-2xl mx-auto leading-relaxed">
            Extract high-quality frames from your videos with precision timing,
            watermarks, and batch processing
        </p>
        <div
            class="flex items-center justify-center gap-6 text-sm text-slate-500"
        >
            <div class="flex items-center gap-2">
                <CheckCircleIcon class="w-4 h-4 text-green-500" />
                <span>Privacy First</span>
            </div>
            <div class="flex items-center gap-2">
                <CheckCircleIcon class="w-4 h-4 text-green-500" />
                <span>Browser Processing</span>
            </div>
            <div class="flex items-center gap-2">
                <CheckCircleIcon class="w-4 h-4 text-green-500" />
                <span>Original Quality</span>
            </div>
        </div>
    </div>
</div>
