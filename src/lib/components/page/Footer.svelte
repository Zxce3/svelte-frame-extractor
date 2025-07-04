<script>
    import { Badge } from "$lib/components/ui/badge/index.js";
    import VideoIcon from "@lucide/svelte/icons/video";
    import SettingsIcon from "@lucide/svelte/icons/settings";
    import SparklesIcon from "@lucide/svelte/icons/sparkles";
    import CheckCircleIcon from "@lucide/svelte/icons/check-circle";
    import { Separator } from "$lib/components/ui/separator/index.js";
</script>

<div class="mt-16 py-12 border-t border-slate-200/60">
    <div class="max-w-4xl mx-auto text-center space-y-6">
        <div class="flex items-center justify-center gap-3">
            <div
                class="w-12 h-12 bg-gradient-to-br from-slate-600 to-slate-800 rounded-xl flex items-center justify-center shadow-lg"
            >
                <VideoIcon class="w-6 h-6 text-white" />
            </div>
            <div class="text-left">
                <h3 class="text-lg font-semibold text-slate-800">
                    Video Frame Extractor
                </h3>
                <p class="text-sm text-slate-500">
                    Professional frame extraction tool
                </p>
            </div>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-3 gap-8 text-sm">
            <div class="space-y-3">
                <h4
                    class="font-semibold text-slate-700 flex items-center gap-2"
                >
                    <CheckCircleIcon class="w-4 h-4 text-green-500" />
                    Privacy & Security
                </h4>
                <ul class="space-y-1 text-slate-600">
                    <li>• Client-side processing only</li>
                    <li>• No data uploaded to servers</li>
                    <li>• Works completely offline</li>
                </ul>
            </div>

            <div class="space-y-3">
                <h4
                    class="font-semibold text-slate-700 flex items-center gap-2"
                >
                    <SparklesIcon class="w-4 h-4 text-purple-500" />
                    Features
                </h4>
                <ul class="space-y-1 text-slate-600">
                    <li>• Multiple extraction methods</li>
                    <li>• Custom watermarking</li>
                    <li>• Batch frame processing</li>
                </ul>
            </div>

            <div class="space-y-3">
                <h4
                    class="font-semibold text-slate-700 flex items-center gap-2"
                >
                    <SettingsIcon class="w-4 h-4 text-blue-500" />
                    Supported Formats
                </h4>
                <ul class="space-y-1 text-slate-600">
                    <li>• Input: MP4, MOV, AVI, WebM</li>
                    <li>• Output: JPEG, PNG</li>
                    <li>• High-quality extraction</li>
                </ul>
            </div>
        </div>

        <Separator class="my-8" />

        <div
            class="flex flex-col md:flex-row items-center justify-between gap-4 text-xs text-slate-500"
        >
            <p>
                © 2025 Video Frame Extractor. Built with modern web
                technologies.
            </p>
            <div class="flex items-center gap-4">
                <Badge variant="outline" class="text-xs">Privacy-First</Badge>
                <Badge variant="outline" class="text-xs">Open Source</Badge>
                <Badge variant="outline" class="text-xs">No Tracking</Badge>
            </div>
        </div>
    </div>
</div>
