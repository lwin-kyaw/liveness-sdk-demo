<script setup lang="ts">
import LivenessUI, { LivenessUIConfig, LivenessError, DisplayMode } from "@web3auth/livenesscheck";
import { ref } from "vue";

const themeRef = ref<"light" | "dark">("light");
const displayModeRef = ref<DisplayMode>("Modal");

const livenessTheme: LivenessUIConfig = {
  defaultLanguage: "de",
  uiColorMode: themeRef.value,
  livenessConfidenceThreashold: 80,
  // displayMode: "Component",
  onCancelled: () => {
    console.log("on analysis cancelled")
  },
  onFailed: (error?: LivenessError) => {
    console.error("onFailed", error);
  },
  onSuccess: (data) => {
    console.log("success fully finished liveness analysis", data);
  },
  onRetry: () => {
    console.log("retrying....");
  },
  onCompleted: (result, params) => {
    console.log("liveness check completed");
    console.log("result", result);
    console.log("params", params);
  },
  whitelabel: {
    appName: "Web3Auth",
    logoLight: "https://images.web3auth.io/web3auth-logo-w.svg",
    logoDark: "https://images.web3auth.io/web3auth-logo-w.svg",
  },
};

const livenessRef = ref<LivenessUI>(new LivenessUI(livenessTheme));
const livenessInitialized = ref<boolean>(false);

function onInitUI() {
  livenessRef.value.init();
  livenessInitialized.value = true;
}

function onThemeChange(e: Event) {
  if (e.target) {
    const target = e.target as HTMLSelectElement;
    themeRef.value = target.value as "light" | "dark";
    livenessRef.value.setUiColorMode(themeRef.value);
  }
}

function onDisplayModeChange(e: Event) {
  if (e.target) {
    const target = e.target as HTMLSelectElement;
    displayModeRef.value = target.value as DisplayMode;
    livenessRef.value.setDisplayMode(displayModeRef.value);
  }
}
</script>

<template>
  <div class="app">
    <div class="header">Liveness Check Demo</div>
    <div style="padding: 0.6rem;">
      <label>Customize Liveness UI Config</label><br/>
      <div class="options-wrapper">
        <div class="options">
          <label>Theme</label>
          <select :value="themeRef" @change="onThemeChange">
            <option value="light">light</option>
            <option value="dark">dark</option>
          </select>
          <label>Display Mode&nbsp;</label>
          <select :value="displayModeRef" @change="onDisplayModeChange">
            <option value="Modal">Modal</option>
            <option value="Component">Component</option>
          </select>
        </div>
        <button type="button" @click="onInitUI">Init Liveness</button>
      </div>
    </div>
    <div id="w3a-lc"></div>
  </div>
</template>

<style scoped>
.app {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

.header {
  width: 100%;
    background: dodgerblue;
    color: white;
    padding: 1rem;
}

.options-wrapper {
  display: flex;
  justify-content: space-between;
  padding: 0.6rem 0;
}
</style>
