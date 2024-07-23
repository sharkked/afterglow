<script>
  import { onMount } from "svelte";
  import { appWindow } from "@tauri-apps/api/window";

  import WindowClose from "../lib/WindowClose.svelte";
  import WindowMinimize from "../lib/WindowMinimize.svelte";

  onMount(() => {
    document.addEventListener("contextmenu", (event) => event.preventDefault());
    document
      .getElementById("titlebar-minimize")
      .addEventListener("click", () => appWindow.minimize());
    document
      .getElementById("titlebar-close")
      .addEventListener("click", () => appWindow.close());
  });
</script>

<div class="titlebar">
  <div
    class="titlebar-drag"
    data-tauri-drag-region
    onclick="appWindow.startDragging"
  ></div>
  <div class="titlebar-button" id="titlebar-minimize">
    <WindowMinimize />
  </div>
  <div class="titlebar-button" id="titlebar-close">
    <WindowClose />
  </div>
</div>

<slot />
