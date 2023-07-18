<script>
    import Snake from "../components/Snake.svelte";
</script>

<div class="page">
    <div class="snake">
        <Snake />
    </div>
</div>


<style>
	:global(body) { /* this will apply to <body> */ margin: 0; padding: 0; }
    :global(header){
        height: 0;
        width: 0;
        margin: 0;
        padding: 0;
    }
    .page{
        display: flex;
        background-color: grey;
        width: 100vw;
        height: 100vh;
    }
    .snake{
        margin: auto;
    }
</style>