<script lang="ts">
    import DownloadIcon from "@lucide/svelte/icons/download";
    import ImageIcon from "@lucide/svelte/icons/image";
    import CheckCircleIcon from "@lucide/svelte/icons/check-circle";
    import { Button } from "$lib/components/ui/button/index.js";
    import {
        Card,
        CardContent,
        CardDescription,
        CardHeader,
        CardTitle,
    } from "$lib/components/ui/card/index.js";
    import { Badge } from "$lib/components/ui/badge/index.js";

    interface ExtractedFrame {
        id: string;
        dataUrl: string;
        timestamp: number;
        selected: boolean;
    }

    interface Props {
        extractedFrames: ExtractedFrame[];
        onToggleFrameSelection: (frameId: string) => void;
        onSelectAllFrames: () => void;
        onDeselectAllFrames: () => void;
        onDownloadSelectedFrames: () => void;
    }

    let {
        extractedFrames,
        onToggleFrameSelection,
        onSelectAllFrames,
        onDeselectAllFrames,
        onDownloadSelectedFrames,
    }: Props = $props();

    const selectedFramesCount = $derived(
        extractedFrames.filter((f) => f.selected).length,
    );

    function formatTime(seconds: number) {
        const mins = Math.floor(seconds / 60);
        const secs = Math.floor(seconds % 60);
        return `${mins}:${secs.toString().padStart(2, "0")}`;
    }
</script>

{#if extractedFrames.length > 0}
    <Card class="shadow-xl bg-white/80 backdrop-blur-sm border-0">
        <CardHeader class="pb-6">
            <div
                class="flex flex-col lg:flex-row lg:items-center justify-between gap-4"
            >
                <div>
                    <CardTitle class="flex items-center gap-3 text-xl">
                        <div
                            class="p-2 bg-gradient-to-br from-orange-500 to-red-600 rounded-lg"
                        >
                            <ImageIcon class="w-5 h-5 text-white" />
                        </div>
                        Extracted Frames
                    </CardTitle>
                    <CardDescription class="text-base mt-2">
                        <span class="font-semibold text-emerald-600">
                            {selectedFramesCount}
                        </span>
                        of
                        <span class="font-semibold">{extractedFrames.length}</span>
                        frames selected
                    </CardDescription>
                </div>
                <div class="flex flex-wrap gap-3">
                    <Button
                        variant="outline"
                        size="sm"
                        onclick={onSelectAllFrames}
                        class="bg-white/70 hover:bg-white hover:shadow-md transition-all duration-200"
                    >
                        Select All
                    </Button>
                    <Button
                        variant="outline"
                        size="sm"
                        onclick={onDeselectAllFrames}
                        class="bg-white/70 hover:bg-white hover:shadow-md transition-all duration-200"
                    >
                        Deselect All
                    </Button>
                    <Button
                        onclick={onDownloadSelectedFrames}
                        class="bg-gradient-to-r from-green-600 to-emerald-600 hover:from-green-700 hover:to-emerald-700 text-white shadow-lg hover:shadow-xl transition-all duration-300 flex items-center gap-2"
                    >
                        <DownloadIcon class="w-4 h-4" />
                        Download Selected
                    </Button>
                </div>
            </div>
        </CardHeader>
        <CardContent>
            <div
                class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 xl:grid-cols-6 2xl:grid-cols-8 gap-4"
            >
                {#each extractedFrames as frame (frame.id)}
                    <div
                        class={`relative group cursor-pointer rounded-xl overflow-hidden border-2 transition-all duration-300 hover:scale-105 hover:shadow-xl ${
                            frame.selected
                                ? "border-blue-500 ring-4 ring-blue-200 shadow-lg"
                                : "border-slate-200 hover:border-slate-300"
                        }`}
                        onclick={() => onToggleFrameSelection(frame.id)}
                        onkeydown={(e) => {
                            if (e.key === "Enter" || e.key === " ") {
                                e.preventDefault();
                                onToggleFrameSelection(frame.id);
                            }
                        }}
                        role="button"
                        tabindex="0"
                    >
                        <img
                            src={frame.dataUrl}
                            alt={`Frame at ${formatTime(frame.timestamp)}`}
                            class="w-full aspect-video object-cover transition-transform duration-300 group-hover:scale-110"
                        />
                        <div
                            class="absolute inset-0 bg-gradient-to-t from-black/60 via-transparent to-transparent opacity-0 group-hover:opacity-100 transition-opacity duration-300 flex items-end justify-center pb-3"
                        >
                            <Badge
                                variant="secondary"
                                class="text-xs font-medium bg-white/90 text-slate-800"
                            >
                                {formatTime(frame.timestamp)}
                            </Badge>
                        </div>
                        <div class="absolute top-3 right-3">
                            <div
                                class={`w-6 h-6 rounded-full backdrop-blur-sm transition-all duration-200 flex items-center justify-center ${
                                    frame.selected
                                        ? "bg-blue-500/90"
                                        : "bg-white/90 hover:bg-slate-100/90"
                                }`}
                            >
                                {#if frame.selected}
                                    <CheckCircleIcon class="w-4 h-4 text-white" />
                                {:else}
                                    <div
                                        class="w-3 h-3 rounded-full border-2 border-slate-400"
                                    ></div>
                                {/if}
                            </div>
                        </div>
                    </div>
                {/each}
            </div>
        </CardContent>
    </Card>
{/if}
