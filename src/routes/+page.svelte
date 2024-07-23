<script>
    import { onMount } from "svelte";
    import PauseIcon from "$lib/PauseIcon.svelte";
    import PlayIcon from "$lib/PlayIcon.svelte";

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

    function handleTogglePlaying(event) {
        if (!player) return;
        event.target.innerHTML = "";
        let playButton = document.querySelector("#play-pause");
        playButton.innerHTML = "";
        if (playing) {
            new PlayIcon({
                target: playButton,
            });
            player.stopVideo();
        } else {
            new PauseIcon({
                target: playButton,
            });
            player.setVolume(volume);
            player.playVideo();
        }
        playing = !playing;
    }

    function handlePlayerReady(event) {
        event.target.playVideo();
        event.target.setVolume(volume);
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
    });
</script>

<svelte:head>
    <script src="https://www.youtube.com/iframe_api"></script>
</svelte:head>

<div id="yt-player"></div>
<main>
    <button
        id="play-pause"
        class="media-controls-button"
        on:click={handleTogglePlaying}
    >
        <PauseIcon id="media-controls-icon" />
    </button>

    <div class="volume-slider">
        <input
            type="range"
            min="0"
            max="100"
            value="50"
            on:input={handleVolumeChanged}
            on:wheel={(event) => {
                if (event.deltaY < 0) {
                    event.target.valueAsNumber += 5;
                } else {
                    event.target.value -= 5;
                }
                handleVolumeChanged(event);
                event.preventDefault();
                event.stopPropagation();
            }}
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
        background: white;
        box-shadow: -80px 0 0 80px var(--c-accent);
        cursor: ew-resize;
        -webkit-appearance: none;
        z-index: 5;
    }
</style>
