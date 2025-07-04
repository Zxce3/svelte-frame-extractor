<script lang="ts">
    import UploadIcon from "@lucide/svelte/icons/upload";
    import SparklesIcon from "@lucide/svelte/icons/sparkles";
    import CheckCircleIcon from "@lucide/svelte/icons/check-circle";
    import ArrowRightIcon from "@lucide/svelte/icons/arrow-right";
    import { Button } from "$lib/components/ui/button/index.js";
    import { Card, CardContent } from "$lib/components/ui/card/index.js";
    import { toast } from "svelte-sonner";

    interface Props {
        onFileSelect: (file: File) => void;
    }

    let { onFileSelect }: Props = $props();

    let fileInputRef = $state<HTMLInputElement>();

    function handleFileSelect(file: File) {
        if (!file.type.startsWith("video/")) {
            toast.error("Invalid file type", {
                description: "Please select a valid video file (MP4, MOV, AVI, etc.)",
            });
            return;
        }

        onFileSelect(file);
        toast.success("Video loaded", {
            description: `${file.name} is ready for frame extraction`,
        });
    }

    function handleDrop(e: DragEvent) {
        e.preventDefault();
        const files = Array.from(e.dataTransfer?.files || []);
        if (files.length > 0) {
            handleFileSelect(files[0]);
        }
    }
</script>

<div class="max-w-4xl mx-auto">
    <Card
        class="border-2 border-dashed border-blue-200 bg-white/70 backdrop-blur-sm shadow-xl hover:shadow-2xl transition-all duration-300 hover:border-blue-300"
    >
        <CardContent class="p-12">
            <div
                class="text-center space-y-8 group"
                role="button"
                tabindex="0"
                ondrop={handleDrop}
                ondragover={(e) => e.preventDefault()}
            >
                <div class="relative">
                    <div
                        class="w-24 h-24 mx-auto bg-gradient-to-br from-blue-500 to-purple-600 rounded-full flex items-center justify-center shadow-lg group-hover:scale-110 transition-transform duration-300"
                    >
                        <UploadIcon class="w-12 h-12 text-white" />
                    </div>
                    <div
                        class="absolute -top-2 -right-2 w-8 h-8 bg-gradient-to-br from-yellow-400 to-orange-500 rounded-full flex items-center justify-center"
                    >
                        <SparklesIcon class="w-4 h-4 text-white" />
                    </div>
                </div>
                <div class="space-y-4">
                    <h3 class="text-2xl font-semibold text-slate-800">
                        Upload Your Video
                    </h3>
                    <Button
                        onclick={() => fileInputRef?.click()}
                        size="lg"
                        class="bg-gradient-to-r from-blue-600 to-purple-600 hover:from-blue-700 hover:to-purple-700 text-lg px-8 py-3 rounded-xl shadow-lg hover:shadow-xl transition-all duration-300"
                    >
                        <span class="flex items-center gap-2">
                            SELECT VIDEO FILE
                            <ArrowRightIcon class="w-5 h-5" />
                        </span>
                    </Button>
                    <p class="text-slate-500">
                        ...or drag and drop a video file anywhere on this area
                    </p>
                </div>
                <div
                    class="grid grid-cols-1 md:grid-cols-3 gap-4 text-sm text-slate-500 max-w-2xl mx-auto"
                >
                    <div
                        class="flex flex-col items-center space-y-2 p-4 bg-slate-50 rounded-lg"
                    >
                        <CheckCircleIcon class="w-5 h-5 text-green-500" />
                        <span class="font-medium">100% Private</span>
                        <span class="text-xs text-center">Never leaves your device</span>
                    </div>
                    <div
                        class="flex flex-col items-center space-y-2 p-4 bg-slate-50 rounded-lg"
                    >
                        <CheckCircleIcon class="w-5 h-5 text-green-500" />
                        <span class="font-medium">Browser Processing</span>
                        <span class="text-xs text-center">No server uploads needed</span>
                    </div>
                    <div
                        class="flex flex-col items-center space-y-2 p-4 bg-slate-50 rounded-lg"
                    >
                        <CheckCircleIcon class="w-5 h-5 text-green-500" />
                        <span class="font-medium">Original Resolution</span>
                        <span class="text-xs text-center">Maintains video quality</span>
                    </div>
                </div>
            </div>
            <input
                bind:this={fileInputRef}
                type="file"
                accept="video/*"
                onchange={(e) => {
                    const target = e.target as HTMLInputElement;
                    if (target?.files?.[0]) {
                        handleFileSelect(target.files[0]);
                    }
                }}
                class="hidden"
            />
        </CardContent>
    </Card>
</div>
