<script lang="ts">
	export let title = '';
</script>

<button class="container" on:click>
	<slot name="image"></slot>
	{#if title}
		<span>{title}</span>
	{/if}
</button>

<style>
	.container {
		display: flex;
		flex-direction: column;
		align-items: center;
		outline: 0;
		height: 58px;
		width: 50px;
		text-align: center;
		background-color: #e8f0fe;
		color: #1967d2;
		border: 2px solid transparent;
		border-radius: 4px;
		font-size: 12px;
		font-weight: bolder;
		cursor: pointer;
		font-weight: 500;
        justify-content: center;
	}

    .container:hover{
        background-color: #D2E3FC;
    }
</style>
