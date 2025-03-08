<script setup lang="ts">
import { ref } from "vue";

const negative = ref<string | null>(null);
const fileInput = ref<HTMLInputElement | null>(null);
const canvas = ref<HTMLElement>();
const invertNegative = ref(false);

function setImage(event) {
  const url = window.URL;
  negative.value = url.createObjectURL(event.target.files[0]);

  const ctx = canvas.value.getContext("2d");
  const img = new Image();
  img.onload = function () {
    canvas.value.width = img.width;
    canvas.value.height = img.height;
    ctx.drawImage(img, 0, 0);
  };
  img.src = negative.value;
}

function clearImage() {
  negative.value = null;
  invertNegative.value = false;
  const canvasContext = canvas.value.getContext("2d");

  canvasContext.clearRect(0, 0, canvas.value.width, canvas.value.height);

  if (fileInput.value) {
    fileInput.value.value = "";
  }
}

function invertColors() {
  if (!negative.value) return;
  invertNegative.value = !invertNegative.value;
}
</script>

<style lang="css">
.inverted-color {
  filter: invert(1);
}

.buttons {
  display: flex;
  justify-content: center;
  width: 100%;
  padding: 8px 0;
}

.actions {
  display: flex;
  justify-content: space-between;
  width: 25%;
}

.buttons-inverted {
  background-color: yellow;
}

button {
  background-color: white;
  outline: none;
  border-radius: 4px;
  border: none;
  box-shadow: 2px 2px 2px rgba(0, 0, 0, 0.2);
  padding: 5px 8px;
  cursor: pointer;
  transition: 0.15s ease;
}

button:hover {
  box-shadow: 3px 3px 2px rgba(0, 0, 0, 0.2);
}

main {
  width: 100%;
  height: 100vh;
}

body {
  padding: 0;
  margin: 0;
}
</style>

<template>
  <main :style="{ backgroundColor: invertNegative ? 'purple' : 'white' }">
    <div class="buttons">
      <input
        ref="fileInput"
        type="file"
        accept="image/png image/jpeg"
        @change="setImage($event)"
      />
      <div class="actions">
        <button
          v-text="'Invert negative'"
          @click="invertColors()"
          :class="{ 'buttons-inverted': invertNegative }"
        />
        <button
          @click="clearImage()"
          v-text="'Clear Image'"
          :class="{ 'buttons-inverted': invertNegative }"
        />
      </div>
    </div>
    <canvas
      style="width: 100%"
      ref="canvas"
      :class="{ 'inverted-color': invertNegative }"
    />
  </main>
</template>
