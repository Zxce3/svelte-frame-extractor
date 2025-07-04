<script lang="ts">
    import JSZip from "jszip";
    import { toast } from "svelte-sonner";

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
        position: "top-left" | "top-right" | "bottom-left" | "bottom-right" | "center";
        opacity: number;
    }

    interface Props {
        videoRef?: HTMLVideoElement;
        canvasRef?: HTMLCanvasElement;
        videoDuration: number;
        extractionMethod: "interval" | "count" | "range";
        intervalSeconds: number;
        frameCount: number;
        startTime: number;
        endTime: number;
        outputFormat: "jpeg" | "png";
        watermark: WatermarkSettings;
        extractedFrames: ExtractedFrame[];
        isProcessing: boolean;
        progress: number;
        onFramesExtracted: (frames: ExtractedFrame[]) => void;
        onProgressUpdate: (progress: number) => void;
        onProcessingStateChange: (isProcessing: boolean) => void;
    }

    let {
        videoRef = $bindable(),
        canvasRef = $bindable(),
        videoDuration,
        extractionMethod,
        intervalSeconds,
        frameCount,
        startTime,
        endTime,
        outputFormat,
        watermark,
        extractedFrames,
        isProcessing,
        progress,
        onFramesExtracted,
        onProgressUpdate,
        onProcessingStateChange,
    }: Props = $props();

    function applyWatermark(
        canvas: HTMLCanvasElement,
        ctx: CanvasRenderingContext2D,
    ) {
        if (!watermark.enabled || !watermark.text?.trim()) {
            console.log("Watermark skipped:", {
                enabled: watermark.enabled,
                text: watermark.text,
            });
            return;
        }

        console.log("Applying watermark:", watermark);

        ctx.save();

        // Set up text properties
        ctx.globalAlpha = watermark.opacity;
        ctx.fillStyle = watermark.color;
        ctx.font = `bold ${watermark.fontSize}px Arial, sans-serif`;
        ctx.textBaseline = "top";
        ctx.textAlign = "start";

        // Add text shadow for better visibility
        ctx.shadowColor = "rgba(0, 0, 0, 0.8)";
        ctx.shadowOffsetX = 2;
        ctx.shadowOffsetY = 2;
        ctx.shadowBlur = 4;

        const text = watermark.text.trim();
        const textMetrics = ctx.measureText(text);
        const textWidth = textMetrics.width;
        const textHeight = watermark.fontSize;

        let x = 0,
            y = 0;
        const padding = 20;

        switch (watermark.position) {
            case "top-left":
                x = padding;
                y = padding;
                break;
            case "top-right":
                x = canvas.width - textWidth - padding;
                y = padding;
                break;
            case "bottom-left":
                x = padding;
                y = canvas.height - textHeight - padding;
                break;
            case "bottom-right":
                x = canvas.width - textWidth - padding;
                y = canvas.height - textHeight - padding;
                break;
            case "center":
                x = (canvas.width - textWidth) / 2;
                y = (canvas.height - textHeight) / 2;
                break;
        }

        // Draw the watermark text
        ctx.fillText(text, x, y);

        console.log("Watermark applied at position:", {
            x,
            y,
            textWidth,
            textHeight,
        });

        ctx.restore();
    }

    async function extractFrame(timestamp: number): Promise<string> {
        return new Promise((resolve, reject) => {
            if (!videoRef || !canvasRef) {
                reject(new Error("Video or canvas element not available"));
                return;
            }

            const canvas = canvasRef;
            const ctx = canvas.getContext("2d");
            if (!ctx) {
                reject(new Error("Canvas context not available"));
                return;
            }

            // Set up timeout to prevent hanging
            const timeout = setTimeout(() => {
                reject(new Error("Frame extraction timed out"));
            }, 5000);

            videoRef.currentTime = timestamp;

            const handleSeeked = () => {
                try {
                    clearTimeout(timeout);

                    if (!videoRef) {
                        reject(new Error("Video element lost during extraction"));
                        return;
                    }

                    // Ensure video has valid dimensions
                    if (videoRef.videoWidth === 0 || videoRef.videoHeight === 0) {
                        reject(new Error("Video has invalid dimensions"));
                        return;
                    }

                    console.log("Drawing frame at timestamp:", timestamp);
                    console.log(
                        "Video dimensions:",
                        videoRef.videoWidth,
                        "x",
                        videoRef.videoHeight,
                    );

                    // Set canvas dimensions to match video
                    canvas.width = videoRef.videoWidth;
                    canvas.height = videoRef.videoHeight;

                    // Clear canvas before drawing
                    ctx.clearRect(0, 0, canvas.width, canvas.height);

                    // Draw the video frame
                    ctx.drawImage(videoRef, 0, 0);
                    console.log("Video frame drawn");

                    // Apply watermark if enabled
                    applyWatermark(canvas, ctx);

                    // Generate data URL
                    const dataUrl = canvas.toDataURL(`image/${outputFormat}`, 0.9);
                    console.log("Data URL generated, length:", dataUrl.length);

                    // Remove event listener
                    videoRef.removeEventListener("seeked", handleSeeked);

                    resolve(dataUrl);
                } catch (error) {
                    clearTimeout(timeout);
                    videoRef?.removeEventListener("seeked", handleSeeked);
                    console.error("Error in handleSeeked:", error);
                    reject(error);
                }
            };

            videoRef.addEventListener("seeked", handleSeeked);
        });
    }

    export async function extractFrames() {
        if (!videoRef) return;

        onProcessingStateChange(true);
        onProgressUpdate(0);
        onFramesExtracted([]);

        try {
            const timestamps: number[] = [];

            // Calculate timestamps based on extraction method
            switch (extractionMethod) {
                case "interval":
                    for (let t = 0; t < videoDuration; t += intervalSeconds) {
                        timestamps.push(t);
                    }
                    break;
                case "count":
                    const step = videoDuration / frameCount;
                    for (let i = 0; i < frameCount; i++) {
                        timestamps.push(i * step);
                    }
                    break;
                case "range":
                    const rangeStep = (endTime - startTime) / frameCount;
                    for (let i = 0; i < frameCount; i++) {
                        timestamps.push(startTime + i * rangeStep);
                    }
                    break;
            }

            const frames: ExtractedFrame[] = [];

            for (let i = 0; i < timestamps.length; i++) {
                const timestamp = timestamps[i];
                try {
                    const dataUrl = await extractFrame(timestamp);

                    frames.push({
                        id: `frame-${i}`,
                        dataUrl,
                        timestamp,
                        selected: true,
                    });

                    onProgressUpdate(((i + 1) / timestamps.length) * 100);
                } catch (error) {
                    console.error(`Failed to extract frame at ${timestamp}s:`, error);
                    toast.error("Frame extraction failed", {
                        description: `Failed to extract frame at ${Math.floor(timestamp)}s`,
                    });
                }
            }

            onFramesExtracted(frames);
            onProcessingStateChange(false);

            if (frames.length > 0) {
                toast.success("Extraction complete", {
                    description: `Successfully extracted ${frames.length} frames`,
                });
            } else {
                toast.error("No frames extracted", {
                    description: "Please check your video file and try again",
                });
            }
        } catch (error) {
            onProcessingStateChange(false);
            console.error("Extraction failed:", error);
            toast.error("Extraction failed", {
                description: "An error occurred during frame extraction",
            });
        }
    }

    export function downloadFrame(frame: ExtractedFrame) {
        const link = document.createElement("a");
        link.download = `frame_${Math.floor(frame.timestamp)}s.${outputFormat}`;
        link.href = frame.dataUrl;
        link.click();
    }

    export async function downloadSelectedFrames() {
        const selectedFrames = extractedFrames.filter((frame) => frame.selected);

        if (selectedFrames.length === 0) {
            toast.error("No frames selected", {
                description: "Please select at least one frame to download",
            });
            return;
        }

        if (selectedFrames.length === 1) {
            downloadFrame(selectedFrames[0]);
            return;
        }

        // Create ZIP file for multiple frames
        const zip = new JSZip();

        selectedFrames.forEach((frame) => {
            const base64Data = frame.dataUrl.split(",")[1];
            zip.file(
                `frame-${Math.floor(frame.timestamp)}s.${outputFormat}`,
                base64Data,
                { base64: true },
            );
        });

        const zipBlob = await zip.generateAsync({ type: "blob" });
        const link = document.createElement("a");
        link.download = "extracted-frames.zip";
        link.href = URL.createObjectURL(zipBlob);
        link.click();

        toast.success("Download started", {
            description: `Downloading ${selectedFrames.length} frames as ZIP archive`,
        });
    }
</script>
