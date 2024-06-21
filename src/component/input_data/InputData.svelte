<script lang="ts">
    import { onMount, tick } from 'svelte';
    import InputWithImage from './InputWithImage.svelte';

    export let classModelName: string = '';
    export let componentType: string = 'skeleton';

    const confirmKeyName = 'Enter';

    let inputEl: HTMLInputElement;
    let divShowInputEl: HTMLButtonElement;
    let inputWidth: number;
    let hiddenSwitch: boolean = false;

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

    // Utility function
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
        <div class="card-body">
            <div class="card-body-guide">Add data simple</div>
            <div class="input-group">
                <InputWithImage>
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
                <InputWithImage>
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
    </div>
{:else}
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
        width: 590px;
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
</style>
