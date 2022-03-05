<script>
    export let col, row, num
</script>

<div class="tile" style="grid-row-start: {row};grid-column-start: {col}; background-color: hsl({50-5.45*(num -1)}, 100%, 50%)">
    <span class="number">{num}</span>
</div>

<style>
    .tile {
        width: 100%;
        height: 100%;
        display: flex;
        justify-content: center;
        align-items: center;
    }

    .number {
        font-size: 2em;
        color: #fff;
        user-select: none;
    }
</style>