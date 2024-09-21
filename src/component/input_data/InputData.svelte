<script lang="ts">
    import { onMount, tick } from 'svelte';
    import InputWithImage from './InputWithImage.svelte';
    import Const, { INPUT_DATA_MODE } from '../../utils/const.svelte';

    export let classModelName: string = '';
    export let componentType: string = 'skeleton';

    const confirmKeyName = 'Enter';

    let inputEl: HTMLInputElement;
    let divShowInputEl: HTMLButtonElement;
    let inputWidth: number;
    let hiddenSwitch: boolean = false;
    let canvasVideoSource: HTMLCanvasElement;
    let mode = INPUT_DATA_MODE.init;
    let videoSource: any;
    let loading = false;
    let videoCanvasSize = {
        height: 265,
        width: 265
    };
    let canvasCameraOption = {
        flip: false,
        fps: 24,
        holdRecord: true,
        delay: 2,
        duration: 6
    };

    // Handle Event
    let handleOnChangeInput = (
        event: Event & { currentTarget: EventTarget & HTMLInputElement }
    ) => {
        if (inputEl.value.length >= 40) {
            event.preventDefault();
        } else {
            changeClassNameValue(inputEl.value);
        }
    };

    let showInputClick = async () => {
        switchHidden();
        // Use tick() to wait the update of DOM for use focus() function
        await tick();
        inputEl.focus();
    };

    let handleOutFocusInput = (
        event: Event & { currentTarget: EventTarget & HTMLInputElement }
    ) => {
        event.currentTarget.value = event.currentTarget.value.trim();
        switchHidden();
    };

    let switchHidden = () => {
        hiddenSwitch = !hiddenSwitch;
    };

    // Draw video
    const drawVideo = () => {
        if (canvasVideoSource) {
            // Draw source
            let ctxSource = canvasVideoSource.getContext('2d')!;
            let scale = videoSource!.videoWidth / videoSource!.videoHeight;

            canvasVideoSource.height = videoCanvasSize.height;
            canvasVideoSource.width = videoCanvasSize.width;

            ctxSource.setTransform(scale, 0, 0, 1, 0, 0);
            let videoAreaPosition = {
                x: -(videoCanvasSize.width * scale - videoCanvasSize.width) / 2,
                y: 0
            };

            if (canvasCameraOption.flip) {
                ctxSource.translate(canvasVideoSource.width, 0);
                ctxSource.scale(-1, 1);
                videoAreaPosition.x = -videoAreaPosition.x;
            }

            ctxSource.drawImage(
                videoSource!,
                0,
                0,
                videoSource!.videoWidth,
                videoSource!.videoHeight,
                videoAreaPosition.x,
                videoAreaPosition.y,
                videoCanvasSize.width,
                videoCanvasSize.height
            );

            setTimeout(drawVideo, 0);
        }
    };

    // Open webcam
    const obtenerVideoCamara = async () => {
        try {
            loading = true;
            const stream = await navigator.mediaDevices.getUserMedia({
                video: true
            });
            videoSource!.srcObject = stream;
            videoSource!.play();
            drawVideo();
        } catch (error) {
            console.log(error);
        }
    };

    // Utility function0.
    let changeClassNameValue = (value: string) => {
        divShowInputEl.innerText = value;
        inputWidth = inputEl.value.length;
    };

    // Life Cycle
    onMount(async () => {
        inputWidth = classModelName.length;
    });
</script>

{#if componentType == 'card'}
    <div class="card">
        <header class="card-header">
            <div style:display={!hiddenSwitch ? 'none' : ''}>
                <input
                    bind:this={inputEl}
                    bind:value={classModelName}
                    style="width: {inputWidth}ch"
                    class="input-card"
                    on:input={handleOnChangeInput}
                    on:blur={handleOutFocusInput}
                    on:keypress={(e) => {
                        if (e.key === confirmKeyName) {
                            e.currentTarget.blur();
                        }
                    }}
                />
                <svg
                    id="edit-icon"
                    role="button"
                    tabindex="0"
                    width="19"
                    height="18"
                    viewBox="0 0 19 18"
                    fill="none"
                    xmlns="http://www.w3.org/2000/svg"
                    aria-label="Edit class name"
                >
                    <path
                        fill-rule="evenodd"
                        clip-rule="evenodd"
                        d="M16.06 0.590005L17.41 1.94C18.2 2.72 18.2 3.99 17.41 4.77L14.64 7.54L4.18 18H0V13.82L10.4 3.41L13.23 0.590005C14.01 -0.189995 15.28 -0.189995 16.06 0.590005ZM2 16L3.41 16.06L13.23 6.23005L11.82 4.82005L2 14.64V16Z"
                        fill="#BDC1C6"
                    ></path>
                </svg>
            </div>
            <button
                bind:this={divShowInputEl}
                class:show-input-hidden={hiddenSwitch}
                class="show-input"
                on:click={showInputClick}
            >
                {classModelName}
                <svg
                    id="edit-icon"
                    role="button"
                    tabindex="0"
                    width="19"
                    height="18"
                    viewBox="0 0 19 18"
                    fill="none"
                    xmlns="http://www.w3.org/2000/svg"
                    aria-label="Edit class name"
                >
                    <path
                        fill-rule="evenodd"
                        clip-rule="evenodd"
                        d="M16.06 0.590005L17.41 1.94C18.2 2.72 18.2 3.99 17.41 4.77L14.64 7.54L4.18 18H0V13.82L10.4 3.41L13.23 0.590005C14.01 -0.189995 15.28 -0.189995 16.06 0.590005ZM2 16L3.41 16.06L13.23 6.23005L11.82 4.82005L2 14.64V16Z"
                        fill="#BDC1C6"
                    ></path>
                </svg>
            </button>
            <button
                class="edit-pen"
                on:click={() => {
                    divShowInputEl.click();
                }}
            >
            </button>
        </header>
        {#if mode == INPUT_DATA_MODE.init}
            <div class="card-body">
                <div class="card-body-guide">Add data simple</div>
                <div class="input-group">
                    <InputWithImage
                        title="Webcam"
                        on:click={() => {
                            mode = INPUT_DATA_MODE.webcam;
                            obtenerVideoCamara();
                        }}
                    >
                        <svg
                            slot="image"
                            class="sample-source-icon"
                            width="24"
                            height="24"
                            viewBox="0 0 24 24"
                            fill="none"
                            xmlns="http://www.w3.org/2000/svg"
                        >
                            <path
                                fill="#1967D2"
                                fill-rule="evenodd"
                                clip-rule="evenodd"
                                d="M18 6V10.48L22 6.5V17.5L18 13.52V14.52V18C18 19.1 17.1 20 16 20H4C2.9 20 2 19.1 2 18V6C2 4.9 2.9 4 4 4H16C17.1 4 18 4.9 18 6ZM16 14.52V9.69V6H4V18H16V14.52Z"
                            ></path>
                        </svg>
                    </InputWithImage>
                    <InputWithImage
                        title="Upload"
                        on:click={() => {
                            mode = INPUT_DATA_MODE.upload;
                        }}
                    >
                        <svg
                            slot="image"
                            class="sample-source-icon"
                            width="24"
                            height="24"
                            viewBox="0 0 24 24"
                            fill="none"
                            xmlns="http://www.w3.org/2000/svg"
                        >
                            <path
                                fill="#1967D2"
                                fill-rule="evenodd"
                                clip-rule="evenodd"
                                d="M11 7.83L8.41 10.41L7 9L12 4L17 9L15.59 10.42L13 7.83V16H11V7.83ZM6 15H4V18C4 19.1 4.9 20 6 20H18C19.1 20 20 19.1 20 18V15H18V18H6V15Z"
                            ></path>
                        </svg>
                    </InputWithImage>
                </div>
            </div>
        {:else if mode == INPUT_DATA_MODE.webcam}
            <!-- svelte-ignore a11y-media-has-caption -->
            <video bind:this={videoSource} style="display: none;" />
            <div class="data-capture-container">
                <div class="webcam-container">
                    <h4 class="data-capture-title">Webcam</h4>
                    <button
                        id="exit-button"
                        style="display: inherit"
                        on:click={() => {
                            mode = INPUT_DATA_MODE.init;
                            videoSource.srcObject.getVideoTracks()[0].stop();
                        }}
                    >
                        <svg
                            width="14"
                            height="14"
                            viewBox="0 0 14 14"
                            fill="none"
                            xmlns="http://www.w3.org/2000/svg"
                        >
                            <path
                                d="M14 1.41L12.59 0L7 5.59L1.41 0L0 1.41L5.59 7L0 12.59L1.41 14L7 8.41L12.59 14L14 12.59L8.41 7L14 1.41Z"
                                fill="#185ABC"
                            ></path>
                        </svg>
                    </button>
                    <div class="webcam">
                        <canvas bind:this={canvasVideoSource} class="videoCanvas"></canvas>
                        <button
                            id="flip-button"
                            class="webcam-button"
                            on:click={() => {
                                canvasCameraOption = {
                                    ...canvasCameraOption,
                                    flip: !canvasCameraOption.flip
                                };
                            }}
                        >
                            <svg
                                id="flip-icon"
                                xmlns="http://www.w3.org/2000/svg"
                                width="18"
                                height="22"
                                viewBox="0 0 20 24"
                                fill="none"
                            >
                                <path
                                    fill-rule="evenodd"
                                    clip-rule="evenodd"
                                    d="M11.1114 24H8.88916V0H11.1114V24ZM0 19.6363V4.36358C0 3.16358 1 2.18176 2.22222 2.18176H6.66667V4.36358H2.22222V19.6363H6.66667V21.8181H2.22222C1 21.8181 0 20.8363 0 19.6363ZM17.7773 8.72711H19.9996V6.54529H17.7773V8.72711ZM13.333 21.8182H15.5552V19.6364H13.333V21.8182ZM17.7773 2.18176V4.36358H19.9996C19.9996 3.16358 18.9996 2.18176 17.7773 2.18176ZM17.7773 17.4544H19.9996V15.2726H17.7773V17.4544ZM15.5552 4.36358H13.333V2.18176H15.5552V4.36358ZM17.7773 13.0909H19.9996V10.9091H17.7773V13.0909ZM19.9996 19.6364C19.9996 20.8364 18.9996 21.8182 17.7773 21.8182V19.6364H19.9996Z"
                                    fill="white"
                                ></path>
                            </svg>
                            <span class="button-text"><!---->Flip Camera<!----></span>
                        </button>
                    </div>
                    <div class="record-container">
                        <div class="record-btn">
                            <button>
                                <span
                                    >{canvasCameraOption.holdRecord
                                        ? 'Hold to record'
                                        : `Record ${canvasCameraOption.duration} seconds`}
                                </span>
                            </button>
                        </div>
                        <button class="settings-button" aria-label="Settings">
                            <svg
                                width="24"
                                height="24"
                                viewBox="0 0 24 24"
                                fill="none"
                                xmlns="http://www.w3.org/2000/svg"
                            >
                                <path
                                    fill-rule="evenodd"
                                    clip-rule="evenodd"
                                    d="M15.3102 21.03C15.2102 21.71 14.5902 22.25 13.8502 22.25H10.1502C9.41023 22.25 8.79023 21.71 8.70023 20.98L8.43023 19.09C8.16023 18.95 7.90023 18.8 7.64023 18.63L5.84023 19.35C5.14023 19.61 4.37023 19.32 4.03023 18.7L2.20023 15.53C1.85023 14.87 2.00023 14.09 2.56023 13.65L4.09023 12.46C4.08023 12.31 4.07023 12.16 4.07023 12C4.07023 11.85 4.08023 11.69 4.09023 11.54L2.57023 10.35C1.98023 9.9 1.83023 9.09 2.20023 8.47L4.05023 5.28C4.39023 4.66 5.16023 4.38 5.84023 4.65L7.65023 5.38C7.91023 5.21 8.17023 5.06 8.43023 4.92L8.70023 3.01C8.79023 2.31 9.41023 1.76 10.1402 1.76H13.8402C14.5802 1.76 15.2002 2.3 15.2902 3.03L15.5602 4.92C15.8302 5.06 16.0902 5.21 16.3502 5.38L18.1502 4.66C18.8602 4.4 19.6302 4.69 19.9702 5.31L21.8102 8.49C22.1702 9.15 22.0102 9.93 21.4502 10.37L19.9302 11.56C19.9402 11.71 19.9502 11.86 19.9502 12.02C19.9502 12.18 19.9402 12.33 19.9302 12.48L21.4502 13.67C22.0102 14.12 22.1702 14.9 21.8202 15.53L19.9602 18.75C19.6202 19.37 18.8502 19.65 18.1602 19.38L16.3602 18.66C16.1002 18.83 15.8402 18.98 15.5802 19.12L15.3102 21.03ZM10.6202 20.25H13.3802L13.7502 17.7L14.2802 17.48C14.7202 17.3 15.1602 17.04 15.6202 16.7L16.0702 16.36L18.4502 17.32L19.8302 14.92L17.8002 13.34L17.8702 12.78L17.8733 12.7531V12.7531C17.9023 12.5027 17.9302 12.2607 17.9302 12C17.9302 11.73 17.9002 11.47 17.8702 11.22L17.8002 10.66L19.8302 9.08L18.4402 6.68L16.0502 7.64L15.6002 7.29C15.1802 6.97 14.7302 6.71 14.2702 6.52L13.7502 6.3L13.3802 3.75H10.6202L10.2502 6.3L9.72023 6.51C9.28023 6.7 8.84023 6.95 8.38023 7.3L7.93023 7.63L5.55023 6.68L4.16023 9.07L6.19023 10.65L6.12023 11.21C6.09023 11.47 6.06023 11.74 6.06023 12C6.06023 12.26 6.08023 12.53 6.12023 12.78L6.19023 13.34L4.16023 14.92L5.54023 17.32L7.93023 16.36L8.38023 16.71C8.81023 17.04 9.24023 17.29 9.71023 17.48L10.2402 17.7L10.6202 20.25ZM15.5002 12C15.5002 13.933 13.9332 15.5 12.0002 15.5C10.0672 15.5 8.50023 13.933 8.50023 12C8.50023 10.067 10.0672 8.5 12.0002 8.5C13.9332 8.5 15.5002 10.067 15.5002 12Z"
                                    fill="#185ABC"
                                ></path>
                            </svg>
                        </button>
                    </div>
                </div>
                <div class="data-captured"></div>
            </div>
        {:else if mode == INPUT_DATA_MODE.upload}
            <div>upload</div>
        {/if}
    </div>
{:else if componentType == 'skeleton'}
    <button class="skeleton-card" on:click> Add a class </button>
{/if}

<style>
    .skeleton-card {
        width: 590px;
        position: relative;
        padding: 30px;
        text-align: center;
        font-size: 18px;
        color: #5f6368;
        cursor: pointer;
        outline: 0;
        border: 0;
        background: transparent;
        overflow: hidden;
        border-radius: 10px;
        font-family: 'Google Sans', Helvetica, Arial, sans-serif;
    }

    .skeleton-card::before {
        content: '';
        position: absolute;
        border: 7px dashed #bdc1c6;
        border-radius: 18px;
        top: -6px;
        bottom: -6px;
        left: -6px;
        right: -6px;
        transition: border 0.3s;
    }

    .skeleton-card:hover {
        color: #1967d2;
    }

    .skeleton-card:hover::before {
        border: 7px dashed #1967d2;
    }

    .card {
        min-width: 590px;
		max-width: 590px;
        box-sizing: border-box;
        background-color: #fff;
        display: block;
        border-radius: 10px;
        font-family: 'Google Sans', Helvetica, Arial, sans-serif;
    }

    .card-header {
        padding: 18px;
        font-size: 18px;
        border-bottom: 1px #ccc solid;
    }

    .card-body {
        width: 100%;
        display: flex;
        flex-direction: column;
        box-sizing: border-box;
        padding: 18px;
    }

    .card-body-guide {
        font-weight: lighter;
        margin-bottom: 12px;
    }

    .show-input:hover {
        cursor: pointer;
        color: #174ea6;
    }

    .input-card {
        font-size: 18px;
        box-sizing: content-box;
        min-width: 2ch;
        max-width: 40ch;
    }

    .show-input {
        height: 20px;
        transition: color 0.3s;
        white-space: pre;
        font-weight: bold;
        unicode-bidi: isolate;
    }

    .show-input-hidden {
        visibility: hidden;
        position: absolute;
    }

    .input-group {
        display: flex;
        flex-direction: row;
        width: 200px;
        gap: 10px;
    }

    .webcam {
        position: relative;
        border-radius: 8px;
        height: 265px;
        width: 265px;
    }

    .videoCanvas {
        border-radius: 8px;
    }
    .webcam-button {
        position: absolute;
        display: flex;
        align-items: center;
        height: 34px;
        padding: 5px 8px;
        background: none;
        border: none;
        border-radius: 5px;
        z-index: 2;
        cursor: pointer;
    }
    #flip-button {
        top: 5px;
        right: 5px;
        flex-direction: row-reverse;
    }
    #flip-icon {
        margin-left: 5px;
    }
    .webcam-button svg {
        z-index: 2;
    }
    .webcam-button svg {
        filter: drop-shadow(0px 1px 2px rgba(60, 64, 67, 0.3));
    }
    .button-text {
        display: none;
        color: #fff;
        font-family: 'Google Sans', Helvetica, Arial, sans-serif;
        font-size: 16px;
        font-weight: 600;
    }
    .webcam-button:hover .button-text {
        display: block;
    }
    .webcam-button:hover {
        background: rgba(0, 0, 0, 0.7);
    }

    .data-capture-container {
        height: 380px;
    }

    .webcam-container {
        height: 100%;
        position: relative;
        box-sizing: border-box;
        padding: 15px;
        float: left;
        width: 50%;
        background-color: #e8f0fe;
        --focus-color: #d84c6f;
    }

    .data-capture-title {
        margin: 0;
        margin-bottom: 15px;
        font-size: 14px;
        font-weight: 600;
        color: #1967d2;
    }

    #exit-button {
        cursor: pointer;
        background: transparent;
        outline: 0;
        border: none;
        position: absolute;
        top: 18px;
        right: 10px;
    }

    .record-btn {
        --color: #fff;
        --background-color: #1967d2;
        --hover-background-color: #185abc;
        --active-background-color: #174ea6;
        --focus-color: #000;
        width: 220px;
        height: 36px;
        margin-top: 12px;
    }

    .record-btn button {
        position: var(--button-position, relative);
        top: 0;
        left: 0;
        min-width: var(--button-min-width, 100%);
        height: 100%;
        cursor: pointer;
        font-size: var(--font-size, 14px);
        font-weight: var(--font-weight, normal);
        text-align: center;
        outline: 0;
        border: 2px solid transparent;
        border-radius: 4px;
        font-weight: 600;
        letter-spacing: 0.3px;
        padding: var(--button-padding, 7px 16px);
        color: var(--color);
        background-color: var(--background-color);
        font-family: 'Google Sans', Helvetica, Arial, sans-serif;
        white-space: var(--button-white-space, nowrap);
        vertical-align: middle;
        display: flex;
        justify-content: space-evenly;
        align-items: center;
    }

    .record-container {
        display: flex;
        justify-content: center;
        align-items: self-end;
    }

    .settings-button {
        outline: 0;
        border: none;
        margin: 0 0 0 14px;
        padding: 0;
        width: auto;
        background-color: transparent;
    }
</style>
