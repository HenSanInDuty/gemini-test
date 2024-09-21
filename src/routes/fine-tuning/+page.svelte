<script>
    import InputData from '../../component/input_data/InputData.svelte';

    let classModelNames = ['Class 1'];
</script>

<div class="container">
    {#each classModelNames as classModelName}
        <InputData {classModelName} componentType='card'></InputData>
    {/each}
    <InputData componentType='skeleton' on:click={()=>{
        classModelNames = [...classModelNames, `Class ${classModelNames.length + 1}`];
    }}></InputData>
</div>

<style>
    .container {
        padding: 80px;
    }
</style>
