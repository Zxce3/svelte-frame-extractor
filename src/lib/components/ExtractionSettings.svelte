<script lang="ts">
    import SettingsIcon from "@lucide/svelte/icons/settings";
    import TypeIcon from "@lucide/svelte/icons/type";
    import SparklesIcon from "@lucide/svelte/icons/sparkles";
    import { Button } from "$lib/components/ui/button/index.js";
    import {
        Card,
        CardContent,
        CardHeader,
        CardTitle,
    } from "$lib/components/ui/card/index.js";
    import { Input } from "$lib/components/ui/input/index.js";
    import { Label } from "$lib/components/ui/label/index.js";
    import * as Select from "$lib/components/ui/select/index.js";
    import { Checkbox } from "$lib/components/ui/checkbox/index.js";
    import { Slider } from "$lib/components/ui/slider/index.js";
    import {
        Tabs,
        TabsContent,
        TabsList,
        TabsTrigger,
    } from "$lib/components/ui/tabs/index.js";
    import { Progress } from "$lib/components/ui/progress/index.js";
    import { Separator } from "$lib/components/ui/separator/index.js";

    interface WatermarkSettings {
        enabled: boolean;
        text: string;
        fontSize: number;
        color: string;
        position: "top-left" | "top-right" | "bottom-left" | "bottom-right" | "center";
        opacity: number;
    }

    interface Props {
        extractionMethod: "interval" | "count" | "range";
        intervalSeconds: number;
        frameCount: number;
        startTime: number;
        endTime: number;
        videoDuration: number;
        outputFormat: "jpeg" | "png";
        watermark: WatermarkSettings;
        isProcessing: boolean;
        progress: number;
        onExtractFrames: () => void;
    }

    let {
        extractionMethod = $bindable(),
        intervalSeconds = $bindable(),
        frameCount = $bindable(),
        startTime = $bindable(),
        endTime = $bindable(),
        videoDuration,
        outputFormat = $bindable(),
        watermark = $bindable(),
        isProcessing,
        progress,
        onExtractFrames,
    }: Props = $props();

    // Local state for UI components
    let outputFormatValue = $state<string[]>([outputFormat]);
    let watermarkPositionValue = $state<string[]>([watermark.position]);
    let fontSizeValue = $state([watermark.fontSize]);
    let opacityValue = $state([watermark.opacity]);

    // Sync changes back to parent
    $effect(() => {
        outputFormat = outputFormatValue[0] as "jpeg" | "png";
    });

    $effect(() => {
        watermark.position = watermarkPositionValue[0] as WatermarkSettings["position"];
    });

    $effect(() => {
        watermark.fontSize = fontSizeValue[0];
    });

    $effect(() => {
        watermark.opacity = opacityValue[0];
    });

    // Sync prop changes to local state
    $effect(() => {
        outputFormatValue = [outputFormat];
    });

    $effect(() => {
        watermarkPositionValue = [watermark.position];
    });

    $effect(() => {
        fontSizeValue = [watermark.fontSize];
    });

    $effect(() => {
        opacityValue = [watermark.opacity];
    });
</script>

<Card class="shadow-xl bg-white/80 backdrop-blur-sm border-0 h-fit">
    <CardHeader class="pb-4">
        <CardTitle class="flex items-center gap-3 text-xl">
            <div class="p-2 bg-gradient-to-br from-emerald-500 to-cyan-600 rounded-lg">
                <SettingsIcon class="w-5 h-5 text-white" />
            </div>
            Extraction Settings
        </CardTitle>
    </CardHeader>
    <CardContent class="space-y-6">
        <Tabs bind:value={extractionMethod}>
            <TabsList class="grid w-full grid-cols-3 bg-slate-100">
                <TabsTrigger
                    value="interval"
                    class="data-[state=active]:bg-white data-[state=active]:shadow-sm"
                >
                    Interval
                </TabsTrigger>
                <TabsTrigger
                    value="count"
                    class="data-[state=active]:bg-white data-[state=active]:shadow-sm"
                >
                    Count
                </TabsTrigger>
                <TabsTrigger
                    value="range"
                    class="data-[state=active]:bg-white data-[state=active]:shadow-sm"
                >
                    Range
                </TabsTrigger>
            </TabsList>

            <TabsContent value="interval" class="space-y-4 mt-6">
                <div class="space-y-2">
                    <Label class="text-sm font-medium">Extract every (seconds)</Label>
                    <Input
                        type="number"
                        min="0.1"
                        step="0.1"
                        bind:value={intervalSeconds}
                        class="bg-white/50"
                    />
                </div>
            </TabsContent>

            <TabsContent value="count" class="space-y-4 mt-6">
                <div class="space-y-2">
                    <Label class="text-sm font-medium">Number of frames</Label>
                    <Input
                        type="number"
                        min="1"
                        bind:value={frameCount}
                        class="bg-white/50"
                    />
                </div>
            </TabsContent>

            <TabsContent value="range" class="space-y-4 mt-6">
                <div class="grid grid-cols-2 gap-3">
                    <div class="space-y-2">
                        <Label class="text-sm font-medium">Start time (s)</Label>
                        <Input
                            type="number"
                            min="0"
                            max={videoDuration}
                            bind:value={startTime}
                            class="bg-white/50"
                        />
                    </div>
                    <div class="space-y-2">
                        <Label class="text-sm font-medium">End time (s)</Label>
                        <Input
                            type="number"
                            min="0"
                            max={videoDuration}
                            bind:value={endTime}
                            class="bg-white/50"
                        />
                    </div>
                </div>
                <div class="space-y-2">
                    <Label class="text-sm font-medium">Number of frames</Label>
                    <Input
                        type="number"
                        min="1"
                        bind:value={frameCount}
                        class="bg-white/50"
                    />
                </div>
            </TabsContent>
        </Tabs>

        <Separator class="my-6" />

        <div class="space-y-6">
            <div class="space-y-2">
                <Label class="text-sm font-medium">Output format</Label>
                <Select.Root type="multiple" bind:value={outputFormatValue}>
                    <Select.Trigger class="bg-white/50">
                        {outputFormat?.toUpperCase() || "Select format"}
                    </Select.Trigger>
                    <Select.Content>
                        <Select.Item value="jpeg">JPEG</Select.Item>
                        <Select.Item value="png">PNG</Select.Item>
                    </Select.Content>
                </Select.Root>
            </div>

            <div class="space-y-4">
                <div class="flex items-center space-x-3">
                    <Checkbox
                        id="watermark"
                        bind:checked={watermark.enabled}
                        class="data-[state=checked]:bg-gradient-to-br data-[state=checked]:from-purple-500 data-[state=checked]:to-pink-600 border-2"
                    />
                    <Label
                        for="watermark"
                        class="flex items-center gap-2 text-sm font-medium cursor-pointer"
                    >
                        <TypeIcon class="w-4 h-4" />
                        Add watermark
                    </Label>
                </div>

                {#if watermark.enabled}
                    <div
                        class="space-y-4 pl-6 border-l-2 border-gradient-to-b from-purple-200 to-pink-200 bg-gradient-to-br from-purple-50 to-pink-50 rounded-r-lg p-4"
                    >
                        <div class="space-y-2">
                            <Label class="text-sm font-medium">Watermark text</Label>
                            <Input bind:value={watermark.text} class="bg-white/70" />
                        </div>
                        <div class="grid grid-cols-2 gap-3">
                            <div class="space-y-2">
                                <Label class="text-sm font-medium">Font size</Label>
                                <Slider
                                    type="multiple"
                                    bind:value={fontSizeValue}
                                    min={12}
                                    max={72}
                                    step={2}
                                    class="py-2"
                                />
                                <span class="text-xs text-slate-500"
                                    >{watermark.fontSize}px</span
                                >
                            </div>
                            <div class="space-y-2">
                                <Label class="text-sm font-medium">Opacity</Label>
                                <Slider
                                    type="multiple"
                                    bind:value={opacityValue}
                                    min={0.1}
                                    max={1}
                                    step={0.1}
                                    class="py-2"
                                />
                                <span class="text-xs text-slate-500">
                                    {Math.round(watermark.opacity * 100)}%
                                </span>
                            </div>
                        </div>
                        <div class="space-y-2">
                            <Label class="text-sm font-medium">Position</Label>
                            <Select.Root type="multiple" bind:value={watermarkPositionValue}>
                                <Select.Trigger class="bg-white/70">
                                    {watermark.position
                                        ?.replace("-", " ")
                                        .replace(/\b\w/g, (l) => l.toUpperCase()) ||
                                        "Select position"}
                                </Select.Trigger>
                                <Select.Content>
                                    <Select.Item value="top-left">Top Left</Select.Item>
                                    <Select.Item value="top-right">Top Right</Select.Item>
                                    <Select.Item value="bottom-left">Bottom Left</Select.Item>
                                    <Select.Item value="bottom-right">Bottom Right</Select.Item>
                                    <Select.Item value="center">Center</Select.Item>
                                </Select.Content>
                            </Select.Root>
                        </div>
                        <div class="space-y-2">
                            <Label class="text-sm font-medium">Color</Label>
                            <Input
                                type="color"
                                bind:value={watermark.color}
                                class="bg-white/70 h-10"
                            />
                        </div>
                    </div>
                {/if}
            </div>

            <Button
                onclick={onExtractFrames}
                disabled={isProcessing}
                class="w-full bg-gradient-to-r from-emerald-600 to-cyan-600 hover:from-emerald-700 hover:to-cyan-700 text-white shadow-lg hover:shadow-xl transition-all duration-300"
                size="lg"
            >
                {#if isProcessing}
                    <div class="flex items-center gap-2">
                        <div
                            class="w-4 h-4 border-2 border-white/30 border-t-white rounded-full animate-spin"
                        ></div>
                        Extracting...
                    </div>
                {:else}
                    <span class="flex items-center gap-2">
                        <SparklesIcon class="w-5 h-5" />
                        Extract Frames
                    </span>
                {/if}
            </Button>

            {#if isProcessing}
                <div class="space-y-3">
                    <Progress value={progress} class="h-2" />
                    <p class="text-sm text-center text-slate-600 font-medium">
                        {Math.round(progress)}% complete
                    </p>
                </div>
            {/if}
        </div>
    </CardContent>
</Card>
