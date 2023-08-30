<script lang="ts">
	let score = 0;
	type availableStatus = 'ALIVE' | 'DEAD' | 'UNKNOWN';
	let status: availableStatus | null = null;
	let loading = false;
	let failed = false;
	let charInfo: {
		id: number;
		name: string;
		location: {
			name: string;
		};
		status: availableStatus;
		image: string;
	} | null = null;
	async function getData() {
		loading = true;
		failed = false;
		const idToFetch = Math.floor(Math.random() * 826) + 1;
		const res = await fetch(`https://rickandmortyapi.com/api/character/${idToFetch}`);
		console.log(`https://rickandmortyapi.com/api/character/${idToFetch}`);
		charInfo = await res.json();
		loading = false;
	}
	function statusHandler(status: availableStatus) {
		if (!charInfo) return;
		if (status === charInfo.status.toUpperCase()) {
			score += 10;
			getData();
			return;
		}
		score = 0;
		failed = true;
	}
</script>

<h1>Morty Mystery</h1>
<!-- <h1>Status : {status ? status : 'apples'}</h1> -->
<button on:click={getData}>Start</button>
<h1>Score : {score}</h1>

<div class="container">
	<div class="characterCardContainer">
		{#if charInfo && !loading}
			<div class="characterCard">
				<img src={charInfo.image} alt={charInfo.name} />
				<div>
					<div>
						{charInfo.name}
					</div>
					<div>
						<span>Last known location:</span>
						<span>{charInfo.location.name}</span>
					</div>
					<div>
						<span>ID:</span>
						<span>{charInfo.id}</span>
					</div>
				</div>
			</div>
		{/if}
	</div>
	<div class="buttonContainer">
		{#if failed}
			<h1>FAILED</h1>
			<button
				on:click={() => {
					failed = false;
					getData();
				}}>TRY AGAIN!</button
			>
		{:else}
			<div>
				<button on:click={() => statusHandler('ALIVE')}>Alive</button>
				<button on:click={() => statusHandler('DEAD')}>Dead</button>
			</div>
			<button on:click={() => statusHandler('UNKNOWN')}>Unknown</button>
		{/if}
	</div>
</div>

{#if loading}
	<div>Loading......</div>
{/if}

<style>
	.container {
		display: flex;
		height: 500px;
	}

	.characterCardContainer {
		width: 50%;
	}
	button {
		all: unset;
		border: 1px solid black;
		border-radius: 12px;
		padding: 8px;
		min-width: 150px;
		width: fit-content;
		height: 100px;
		cursor: pointer;
		display: flex;
		justify-content: center;
		align-items: center;
		font-size: 1.5rem;
	}

	.buttonContainer {
		width: 50%;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		gap: 50px;
	}

	.buttonContainer > div {
		display: flex;
		gap: 100px;
	}

	.buttonContainer > div > button:nth-child(1) {
		background-color: rgb(170, 195, 227);
	}
	.buttonContainer > div > button:nth-child(2) {
		background-color: rgb(187, 237, 182);
	}
	.buttonContainer > button {
		background-color: rgb(94, 94, 94);
	}

	.characterCard {
		background-color: #303b5b;
		margin: auto;
		display: flex;
		width: 50%;
		flex-direction: column;
		gap: 10px;
		border-radius: 16px;
		width: 300px;
		height: fit-content;
		/* box-sizing: border-box; */
		padding-bottom: 20px;
	}

	.characterCard > div {
		display: flex;
		flex-direction: column;
		justify-content: space-between;
		gap: 15px;
		padding-left: 20px;
	}

	.characterCard > div > div {
		display: flex;
		gap: 3px;
		flex-direction: column;
	}

	.characterCard > div > div:nth-child(1) {
		font-size: 1.5rem;
	}

	.characterCard > div > div:nth-child(2) > span:nth-child(1),
	.characterCard > div > div:nth-child(3) > span:nth-child(1) {
		color: #9b9da0;
	}

	/* .characterCard > div > div:nth-child(1) > div {
		display: flex;
		align-items: center;
		gap: 10px;
	} */

	.characterCard > img {
		height: 100%;
		border-top-right-radius: 16px;
		border-top-left-radius: 16px;
		border-bottom-left-radius: 0;
		border-bottom-right-radius: 0;
	}
</style>
