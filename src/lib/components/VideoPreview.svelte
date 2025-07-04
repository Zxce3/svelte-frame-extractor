<script lang="ts">
    import VideoIcon from "@lucide/svelte/icons/video";
    import { Badge } from "$lib/components/ui/badge/index.js";
    import {
        Card,
        CardContent,
        CardDescription,
        CardHeader,
        CardTitle,
    } from "$lib/components/ui/card/index.js";
    import { toast } from "svelte-sonner";

    interface Props {
        video: File;
        videoUrl: string;
        videoDuration: number;
        onVideoLoad: (duration: number) => void;
        videoRef?: HTMLVideoElement;
        canvasRef?: HTMLCanvasElement;
    }

    let { video, videoUrl, videoDuration, onVideoLoad, videoRef = $bindable(), canvasRef = $bindable() }: Props = $props();

    function formatTime(seconds: number) {
        const mins = Math.floor(seconds / 60);
        const secs = Math.floor(seconds % 60);
        return `${mins}:${secs.toString().padStart(2, "0")}`;
    }

    function handleVideoLoad() {
        if (videoRef) {
            onVideoLoad(videoRef.duration);
        }
    }
</script>

<Card class="shadow-xl bg-white/80 backdrop-blur-sm border-0">
    <CardHeader class="pb-4">
        <div class="flex items-center justify-between">
            <div>
                <CardTitle class="flex items-center gap-3 text-xl">
                    <div
                        class="p-2 bg-gradient-to-br from-purple-500 to-pink-600 rounded-lg"
                    >
                        <VideoIcon class="w-5 h-5 text-white" />
                    </div>
                    Video Preview
                </CardTitle>
                <CardDescription class="text-base mt-2">
                    <span class="font-medium text-slate-700">{video.name}</span>
                    <span class="mx-2">•</span>
                    <span class="text-slate-500">
                        Duration: {formatTime(videoDuration)}
                    </span>
                </CardDescription>
            </div>
            <Badge variant="secondary" class="px-3 py-1">Ready</Badge>
        </div>
    </CardHeader>
    <CardContent class="pb-6">
        <div class="relative group">
            <video
                bind:this={videoRef}
                src={videoUrl}
                controls
                preload="metadata"
                crossorigin="anonymous"
                class="w-full rounded-xl shadow-lg group-hover:shadow-xl transition-shadow duration-300"
                onloadedmetadata={handleVideoLoad}
                onloadeddata={() => {
                    console.log("Video loaded successfully");
                    if (videoRef) {
                        console.log(
                            `Video dimensions: ${videoRef.videoWidth}x${videoRef.videoHeight}`,
                        );
                    }
                }}
                onerror={(e) => {
                    console.error("Video loading error:", e);
                    toast.error("Video loading failed", {
                        description: "Failed to load the video file",
                    });
                }}
            >
                <track kind="captions" />
            </video>
            <canvas bind:this={canvasRef} class="hidden"></canvas>
        </div>
    </CardContent>
</Card>
