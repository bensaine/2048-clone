<script>
	import { onMount } from "svelte"
	import Tile from "./Tile.svelte"
	export let dimensions, score
	let tiles = []

	onMount(() => {
		for (let i = 0; i < dimensions; i++) {
			tiles.push([])
			for (var j = 0; j < dimensions; j++) {
				tiles[i][j] = 0
			}
		}

		addRandomTile()
		addRandomTile()
	})

    function lose() {
        alert("You lose! Score: " + score)
    }

	function addRandomTile() {
		const empty = getEmptyTiles()
        if (empty.length == 0) {
            lose()
            return
        }
		const random = Math.floor(Math.random() * empty.length)
		let row = empty[random][0]
		let col = empty[random][1]
		addTile(col, row, Math.random() > 0.5 ? 2 : 4)
	}

	function addTile(col, row, num) {
		tiles[row][col] = num
	}

	function getEmptyTiles() {
		let empty = []
		for (let i = 0; i < dimensions; i++) {
			for (var j = 0; j < dimensions; j++) {
				if (tiles[i][j] == 0) {
					empty.push([i, j])
				}
			}
		}
		return empty
	}

	function getCol(col) {
		return tiles.map((x) => x[col])
	}

	function moveX(dir) {
		for (let i = 0; i < dimensions; i++) {
			tiles[i] = squash(tiles[i], dir)
		}
		addRandomTile()
	}

	function moveY(dir) {
		let cols = []
		for (let i = 0; i < dimensions; i++) {
			cols[i] = squash(getCol(i), dir)
		}
		for (let i = 0; i < dimensions; i++) {
			for (let j = 0; j < dimensions; j++) {
				tiles[j][i] = cols[i][j]
			}
		}
		addRandomTile()
	}

	function squash(arr, dir) {
		arr = slide(arr, dir)
		for (let i = 0; i < dimensions / 4; i++) {
			arr = merge(arr, dir)
			arr = slide(arr, dir)
		}
		return arr
	}

	function slide(arr, dir) {
		arr = arr.filter((tile) => tile)
		let empty = Array(dimensions - arr.length).fill(0)
		arr = dir > 0 ? empty.concat(arr) : arr.concat(empty)
		return arr
	}

	function merge(arr) {
		for (let i = dimensions; i > 0; i--) {
			let a = arr[i]
			let b = arr[i - 1]
			if (a == b) {
				arr[i] = a * 2
				arr[i - 1] = 0
                score += a * 2
			}
		}
		return arr
	}

	function handleKey(e) {
		switch (e.key) {
			case "ArrowDown":
				moveY(1)
				break

			case "ArrowUp":
				moveY(-1)
				break

			case "ArrowLeft":
				moveX(-1)
				break

			case "ArrowRight":
				moveX(1)
				break

			default:
				break
		}
	}
</script>

<main>
	<div class="grid" style="grid-template-columns: repeat({dimensions}, 1fr); grid-template-rows: repeat({dimensions}, 1fr);">
		{#each tiles as row, y}
			{#each row as tile, x}
				{#if tile > 0}
					<Tile col={x + 1} row={y + 1} num={tile} />
				{/if}
			{/each}
		{/each}
	</div>
</main>
<svelte:window on:keydown={handleKey} />

<style>
	.grid {
		display: grid;
		position: relative;
		width: 500px;
		height: 500px;
		margin: auto;
		grid-gap: 5px;
		padding: 5px;
		background-color: #eee;
	}
</style>
