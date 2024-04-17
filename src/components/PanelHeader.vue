<script setup>
import Button from "primevue/button";
import { useUIStore } from "@/stores/ui";

const ui = useUIStore();
</script>

<template lang="pug">
Button.panel-toggler.left-panel-toggler(@click="ui.showLeftPanel")
  font-awesome-icon(icon="fa-solid fa-gears")
.ui-by-pixio
  |ProCraft Entperise Image Generation Tools (beta)
Button.panel-toggler.right-panel-toggler(@click="ui.showRightPanel")
  font-awesome-icon(icon="fa-solid fa-images")
</template>

<style scoped>
.ui-by-pixio {
  position: fixed;
  top: 16px;
  left: 50%;
  transform: translateX(-50%);
  font-weight: 700;
  color: #64748b;
  z-index: 2; 
}
.panel-toggler {
  position: fixed;
  z-index: 1;
  top: 16px;
  background-color: lightgray;
}

.right-panel-toggler {
  right: 0px;
  padding: 10px 20px 10px 10px;
  border-radius: 5px 0px 0px 5px;
  border-right: none;
}

.left-panel-toggler {
  left: 0px;
  padding: 10px 10px 10px 20px;
  border-radius: 0px 5px 5px 0px;
  border-left: none;
}

@media (max-width: 600px) {
  .panel-toggler {
    top: 0px;
    border-top: none;
  }

  .left-panel-toggler {
    border-radius: 0px 0px 5px 0px;
  }

  .right-panel-toggler {
    border-radius: 0px 0px 0px 5px;
  }
}

.top-padding {
  min-height: 40px;
}
</style>
