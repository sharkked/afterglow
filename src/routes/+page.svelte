<script>
    import { onMount } from "svelte";
    import MediaPause from "$lib/MediaPause.svelte";
    import MediaPlay from "$lib/MediaPlay.svelte";

    let player;
    let playerState = null;
    let initialVideoId = "jfKfPfyJRdk";
    let autoplay = true;
    let volume = 50;

    let ytPlayerId = "yt-player";
    let ytPlayerVolume = volume;
    let playing = autoplay;

    function handleVolumeChanged(event) {
        let volume = event.target.value;
        if (!player) return;
        if (playerState == 1) {
            ytPlayerVolume = volume;
            player.setVolume(ytPlayerVolume);
        }
    }

    function startPlaying() {
        if (!player) return;
        let playButton = document.querySelector("#play-pause");
        playButton.innerHTML = "";
        new MediaPause({
            target: playButton,
        });
        player.playVideo();
        document.documentElement.setAttribute("data-playing", true);
    }

    function stopPlaying() {
        if (!player) return;
        let playButton = document.querySelector("#play-pause");
        playButton.innerHTML = "";
        new MediaPlay({
            target: playButton,
        });
        player.stopVideo();
        document.documentElement.removeAttribute("data-playing");
    }

    function handleScroll(event) {
        let volumeSlider = document.querySelector("input[type='range']");
        if (event.deltaY < 0) {
            volumeSlider.valueAsNumber += 5;
        } else {
            volumeSlider.value -= 5;
        }
        volume = volumeSlider.value;
        if (!player) return;
        ytPlayerVolume = volume;
        player.setVolume(ytPlayerVolume);
        event.stopPropagation();
    }

    function handleTogglePlaying(event) {
        if (!player) return;
        if (playing) {
            stopPlaying();
        } else {
            startPlaying();
        }
        playing = !playing;
    }

    function handlePlayerReady(event) {
        event.target.setVolume(volume);
        startPlaying();
    }

    function handlePlayerStateChanged(event) {
        playerState = event.data;
    }

    onMount(() => {
        window.onYouTubeIframeAPIReady = () => {
            player = new YT.Player(ytPlayerId, {
                height: "1",
                width: "1",
                videoId: initialVideoId,
                playerVars: { autoplay: 1 },
                events: {
                    onReady: handlePlayerReady,
                    onStateChange: handlePlayerStateChanged,
                },
            });
        };

        window.onwheel = handleScroll;
    });
</script>

<svelte:head>
    <script src="https://www.youtube.com/iframe_api"></script>
</svelte:head>

<main>
    <div id="yt-player"></div>
    <div id="app-background"></div>
    <button
        id="play-pause"
        class="media-controls-button"
        on:click={handleTogglePlaying}
    >
        <MediaPause id="media-controls-icon" />
    </button>

    <div class="volume-slider">
        <input
            type="range"
            min="0"
            max="100"
            value="50"
            on:input={handleVolumeChanged}
            class="slider"
            id="volume-slider"
        />
    </div>
</main>

<style>
    #yt-player {
        pointer-events: none;
        position: absolute;
        visibility: hidden;
    }

    main {
        height: 100%;
        display: flex;
        flex-direction: column;
        align-items: center;
        place-content: center;
    }

    #app-background {
        display: block;
        position: absolute;
        top: 0;
        bottom: 0;
        left: 0;
        right: 0;
        pointer-events: none;
        background: rgb(114, 81, 35);
        background: radial-gradient(
            circle at 50% 120% in hsl,
            rgb(88, 52, 22) 0%,
            rgb(82, 30, 26) 50%,
            rgba(53, 18, 36, 1) 69%,
            rgba(2, 0, 36, 1) 110%
        );
        z-index: -1;
        transition: opacity 0.2s;
        opacity: 0;
    }

    .media-controls-button {
        width: 4rem;
        height: 4rem;
        border-radius: 100%;
        color: var(--c-foreground);
        background-color: transparent;
        border: 2px solid var(--c-foreground);
        cursor: pointer;
        transition: all 0.25s;
    }

    .media-controls-button:hover {
        transform: scale(1.05);
    }

    .media-controls-button:active {
        transition: all ease-out 0.05s;
        transform: scale(0.95);
    }

    .volume-slider {
        position: relative;
        top: 1rem;
        height: 0rem;
    }

    input[type="range"] {
        height: 4px;
        width: 6rem;
        border-radius: 4px;
        background-color: transparent;
        overflow: hidden;
        outline: 2px solid var(--c-foreground);
        appearance: none;
        -webkit-appearance: none;
    }

    input[type="range"]::-webkit-slider-runnable-track {
        height: 10px;
        margin-top: -1px;
        color: var(--c-accent);
        -webkit-appearance: none;
    }

    input[type="range"]::-webkit-slider-thumb {
        width: 10px;
        height: 15px;
        background: var(--c-foreground);
        box-shadow: -80px 0 0 80px var(--c-accent);
        cursor: ew-resize;
        -webkit-appearance: none;
        z-index: 5;
    }
</style>
