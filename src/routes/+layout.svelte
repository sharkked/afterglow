<script>
    import { appWindow } from "@tauri-apps/api/window";
    import { onMount } from "svelte";

    onMount(() => {
        document.addEventListener("contextmenu", (event) =>
            event.preventDefault(),
        );
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
        <img
            src="https://api.iconify.design/mdi:window-minimize.svg"
            alt="minimize"
        />
    </div>
    <div class="titlebar-button" id="titlebar-close">
        <img src="https://api.iconify.design/mdi:close.svg" alt="close" />
    </div>
</div>

<slot />
