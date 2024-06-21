<script lang="ts">
    export let title="";
</script>

<div>
    {#if title}
    <span>{title}</span>
    {/if}
    <slot name="image"></slot>
</div>

<style>

</style>