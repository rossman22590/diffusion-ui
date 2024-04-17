<script setup>
import { toRef, watch, ref } from "vue";
import PromptInput from "@/components/PromptInput.vue";
import FileUploadButton from "@/components/FileUploadButton.vue";
import ImageEditor from "@/components/editor/ImageEditor.vue";
import LoadConfigButton from "@/components/LoadConfigButton.vue";
import Button from "primevue/button";
import Slider from "primevue/slider";
import {
  newDrawing,
  renderCanvas,
  updateDrawLayerOpacity,
} from "@/actions/editor";

import { useBackendStore } from "@/stores/backend";
import { useEditorStore } from "@/stores/editor";
import { useInputStore } from "@/stores/input";
import { useUIStore } from "@/stores/ui";

const backend = useBackendStore();
const editor = useEditorStore();
const input = useInputStore();
const ui = useUIStore();

watch(toRef(backend, "strength"), function () {
  // Modify the opacity of the draw layer depending on strength
  updateDrawLayerOpacity();

  // Rerender Canvas
  renderCanvas();
});

watch(toRef(editor, "mode"), function (mode) {
  console.log(`editor mode changed to ${mode}`);

  const possible_modes = backend.getAllowedModes(mode);
  backend.changeFunctionForModes(possible_modes);
});

const styles = [
  { img: "https://cdn.discordapp.com/attachments/1064936600137646274/1124798230316716092/3107212523-1.png", prompt: "Cute small panda sitting in a movie theater eating chicken wiggs watching a movie ,unreal engine, cozy indoor lighting, artstation, detailed, digital painting,cinematic,character design by mark ryden and pixar and hayao miyazaki, unreal 5, daz, hyperrealistic, octane render" },
  { img: "https://cdn.discordapp.com/attachments/988624045786427452/1124551201913061458/3361226116-1.png", prompt: "close up Portrait photo of muscular bearded guy in a worn mech suit, ((light bokeh)), intricate, (steel metal [rust]), elegant, sharp focus, photo by greg rutkowski, soft lighting, vibrant colors, masterpiece, ((streets)), detailed face" },
  { img: "https://image.civitai.com/xG1nkqKTMzGDvpLrqFT7WA/d3e6088f-b583-4e7a-7d37-49ed4b706200/width=1920/00015.jpeg", prompt: "A powerful portrait of The Joker, <lora:Joker:0.2> stunning quality, (standing in a forest), Canon EOS R, 50mm lens, prime lens, masterpiece, deep depth of field, sharp focus, (intricate textures:1.2), (dream-like atmosphere:1.2), high quality, 8k, stunning quality, (haze:1.3), (detailed haze:1.3)" },
  { img: "https://image.civitai.com/xG1nkqKTMzGDvpLrqFT7WA/4bf1ae9d-6b62-4925-88c8-8d9827e32f7e/width=1440/00032-1516563023.jpeg", prompt: "Unparalleled masterpiece, (photorealistic:1.4), best quality, beautiful lighting, (hot spring), (extremely detailed 8k wallpaper), full shot landscape photo of the most beautiful artwork in the world, cloudy sky background lush landscape house and trees illustration concept art" },
];

const selectStyle = (prompt) => {
  const promptInput = backend.findInput('prompt');
  if (promptInput) {
    promptInput.value = prompt;
  }
};


</script>

<template lang="pug">
.flex.flex-column.gap-3
      template(v-if="backend.needs_gradio_config")
        LoadConfigButton
      template(v-else)
        .flex.flex-row.justify-content-center.flex-wrap
          div(v-if="!editor.isEditMode && !backend.isImg2ImgMode", v-for="style in styles", :key="style.prompt", class="image-card")
            img(:src="style.img", @click="selectStyle(style.prompt)", class="style-image")
        template(v-if="!editor.has_image && !input.seed_is_set && backend.hasInput('prompt')")
          .flex.flex-column.align-items-center
            .enter-a-prompt
              | Enter a prompt:
        PromptInput
        div(v-show="backend.has_img2img_mode")
          div(v-show="editor.has_image")
            ImageEditor
            .main-slider(v-if="backend.hasInput('strength')", :class="{visible: ui.show_strength_slider}")
              .flex.flex-row.justify-content-center
                .slider-label.align-items-left(title="At low strengths, the initial image is not modified much")
                  | Low variations
                Slider.align-items-center(v-model="backend.strength_input.value" :min="0" :max="1" :step="0.02" v-tooltip.bottom="{ value: 'Strength:' + backend.strength}")
                .slider-label.align-items-left(title="At a strength of 1, what was previously in the zone is ignored")
                  | Ignore previous
          template(v-if="!editor.has_image")
            .flex.flex-column.align-items-center
              .or(v-if="backend.hasInput('prompt')") OR
              FileUploadButton.main-slider
            .flex.flex-column.align-items-center
              .or OR
              Button.p-button-secondary.p-button-outlined.p-button-sm.p-button-text(@click="newDrawing")
                font-awesome-icon(icon="fa-solid fa-paintbrush")
                | draw something
</template>
  
    
    <style scoped>
    .enter-a-prompt {
      font-weight: 700;
      color: #64748b;
    }
    .api-url-value[data-v-3c519c07] {
  border-radius: 5px;
  background-color: gray;
  color: #fff;
  padding: 5px;
  font-size: small;
  max-width: 300px;
  overflow: none;
  display: none!important;
}
    :deep() .p-button-secondary:not(.p-disabled):hover {
      background-color: transparent !important;
      color: #64748b !important;
      border-color: transparent !important;
      text-decoration: underline !important;
    }
    
    :deep() .p-button-secondary:not(.p-disabled):focus {
      box-shadow: none;
    }
    
    :deep() .p-button-secondary {
      font-weight: 700;
    }
    
    .fa-paintbrush {
      margin-right: 3px;
    }
    
    .or {
      font-size: 12px;
      font-weight: lighter;
    }
    
    .main-slider {
      padding-top: 10px;
      margin-top: auto 20px;
      visibility: hidden;
    }
    
    .main-slider.visible {
      visibility: initial;
    }
    
    .slider-label {
      font-weight: bold;
      text-align: center;
    }
    
    :deep() .p-slider-horizontal {
      margin: 10px 20px 10px 20px;
      color: black;
      height: 0.15rem !important;
      width: 80%;
      max-width: 300px;
    }
    
    :deep() .p-slider-range {
      background: black !important;
    }
    
    :deep() .p-slider-handle {
      border: 4px solid #273767 !important;
    }
    
    .image-card {
      border: 1px solid #dfe1e6;
      border-radius: 5px;
      box-shadow: 0px 3px 6px #00000029;
      transition: all 0.3s ease-in-out;
      margin: 10px;
      overflow: hidden;
    }
    
    .image-card:hover {
      transform: scale(1.03);
      border: 1px solid #64748b;
    }
    
    .style-image {
      height: 100%;
      width: 200px;
      object-fit: cover;
    }
    </style>
  