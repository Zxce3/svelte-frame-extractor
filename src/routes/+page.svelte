<script lang="ts">
    import RotateCcwIcon from "@lucide/svelte/icons/rotate-ccw";
    import { Button } from "$lib/components/ui/button/index.js";
    import VideoUpload from "$lib/components/VideoUpload.svelte";
    import VideoPreview from "$lib/components/VideoPreview.svelte";
    import ExtractionSettings from "$lib/components/ExtractionSettings.svelte";
    import ExtractedFrames from "$lib/components/ExtractedFrames.svelte";
    import VideoProcessor from "$lib/components/VideoProcessor.svelte";

    interface ExtractedFrame {
        id: string;
        dataUrl: string;
        timestamp: number;
        selected: boolean;
    }

    interface WatermarkSettings {
        enabled: boolean;
        text: string;
        fontSize: number;
        color: string;
        position:
            | "top-left"
            | "top-right"
            | "bottom-left"
            | "bottom-right"
            | "center";
        opacity: number;
    }

    // State variables using $state rune
    let video = $state<File | null>(null);
    let videoUrl = $state<string>("");
    let videoDuration = $state<number>(0);
    let extractedFrames = $state<ExtractedFrame[]>([]);
    let isProcessing = $state(false);
    let progress = $state(0);
    let extractionMethod = $state<"interval" | "count" | "range">("interval");
    let intervalSeconds = $state(1);
    let frameCount = $state(10);
    let startTime = $state(0);
    let endTime = $state(0);
    let outputFormat = $state<"jpeg" | "png">("jpeg");
    let watermark = $state<WatermarkSettings>({
        enabled: false,
        text: "Frame Extractor",
        fontSize: 24,
        color: "#ffffff",
        position: "bottom-right",
        opacity: 0.8,
    });

    // DOM element references
    let videoRef = $state<HTMLVideoElement>();
    let canvasRef = $state<HTMLCanvasElement>();
    let videoProcessor: VideoProcessor;

    function handleFileSelect(file: File) {
        video = file;
        const url = URL.createObjectURL(file);
        videoUrl = url;
        extractedFrames = [];
    }

    function handleVideoLoad(duration: number) {
        videoDuration = duration;
        endTime = duration;
    }

    function handleFramesExtracted(frames: ExtractedFrame[]) {
        extractedFrames = frames;
    }

    function handleProgressUpdate(newProgress: number) {
        progress = newProgress;
    }

    function handleProcessingStateChange(processing: boolean) {
        isProcessing = processing;
    }

    function handleExtractFrames() {
        videoProcessor?.extractFrames();
    }

    function toggleFrameSelection(frameId: string) {
        extractedFrames = extractedFrames.map((frame) =>
            frame.id === frameId
                ? { ...frame, selected: !frame.selected }
                : frame,
        );
    }

    function selectAllFrames() {
        extractedFrames = extractedFrames.map((frame) => ({
            ...frame,
            selected: true,
        }));
    }

    function deselectAllFrames() {
        extractedFrames = extractedFrames.map((frame) => ({
            ...frame,
            selected: false,
        }));
    }

    function downloadSelectedFrames() {
        videoProcessor?.downloadSelectedFrames();
    }

    function startOver() {
        video = null;
        videoUrl = "";
        extractedFrames = [];
        progress = 0;
    }
</script>

<VideoProcessor
    bind:this={videoProcessor}
    bind:videoRef
    bind:canvasRef
    {videoDuration}
    {extractionMethod}
    {intervalSeconds}
    {frameCount}
    {startTime}
    {endTime}
    {outputFormat}
    {watermark}
    {extractedFrames}
    {isProcessing}
    {progress}
    onFramesExtracted={handleFramesExtracted}
    onProgressUpdate={handleProgressUpdate}
    onProcessingStateChange={handleProcessingStateChange}
/>

{#if !video}
    <VideoUpload onFileSelect={handleFileSelect} />
{/if}

{#if video}
    <div class="grid lg:grid-cols-3 gap-8">
        <div class="lg:col-span-2">
            <VideoPreview
                {video}
                {videoUrl}
                {videoDuration}
                onVideoLoad={handleVideoLoad}
                bind:videoRef
                bind:canvasRef
            />
        </div>

        <div class="lg:col-span-1">
            <ExtractionSettings
                bind:extractionMethod
                bind:intervalSeconds
                bind:frameCount
                bind:startTime
                bind:endTime
                {videoDuration}
                bind:outputFormat
                bind:watermark
                {isProcessing}
                {progress}
                onExtractFrames={handleExtractFrames}
            />
        </div>
    </div>
{/if}

<!-- Extracted Frames -->
<ExtractedFrames
    {extractedFrames}
    onToggleFrameSelection={toggleFrameSelection}
    onSelectAllFrames={selectAllFrames}
    onDeselectAllFrames={deselectAllFrames}
    onDownloadSelectedFrames={downloadSelectedFrames}
/>

<!-- Reset Button -->
{#if video}
    <div class="text-center py-8">
        <Button
            variant="outline"
            onclick={startOver}
            class="bg-white/70 hover:bg-white hover:shadow-lg transition-all duration-300 flex items-center gap-2 px-6 py-3"
            size="lg"
        >
            <RotateCcwIcon class="w-5 h-5" />
            Start Over
        </Button>
    </div>
{/if}
