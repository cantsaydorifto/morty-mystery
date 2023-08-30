<script lang="ts">
	let addPoint = false;
	let score = 0;
	type availableStatus = 'ALIVE' | 'DEAD' | 'UNKNOWN';
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
			addPoint = true;
			setTimeout(() => {
				addPoint = false;
			}, 500);
			getData();
			return;
		}
		score = 0;
		failed = true;
	}
</script>

<main>
	<h1>Morty Mystery</h1>
	<!-- <h1>Status : {status ? status : 'apples'}</h1> -->
	<h1 class={`score ${failed ? 'failed' : ''}`}>
		Score : <span>{score}</span>
		{#if addPoint}
			<span class="addPoint">{'+10'}</span>
		{/if}
	</h1>

	<div class="container">
		{#if loading && !charInfo}
			<div class="characterCard loading">
				<p>Loading</p>
			</div>
			<div class="buttonContainer">Loading....</div>
		{/if}
		{#if !charInfo}
			{#if !loading}
				<button class="start" on:click={getData}>Start</button>
			{/if}
		{:else}
			<div class="characterCardContainer">
				<div class="characterCard">
					{#if failed}
						<span class={`characterStatus ${charInfo.status.toLocaleLowerCase()}`}
							>{charInfo.status.toUpperCase()}</span
						>
					{/if}
					{#if charInfo && !loading}
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
					{/if}
				</div>
			</div>
			<div class="buttonContainer">
				{#if failed}
					<button
						class="tryAgain"
						on:click={() => {
							failed = false;
							getData();
						}}>TRY AGAIN!</button
					>
				{:else}
					<div>
						<button disabled={loading} on:click={() => statusHandler('ALIVE')}>Alive</button>
						<button disabled={loading} on:click={() => statusHandler('DEAD')}>Dead</button>
					</div>
					<button disabled={loading} on:click={() => statusHandler('UNKNOWN')}>Unknown</button>
				{/if}
			</div>
		{/if}
	</div>
</main>

<style>
	main {
		width: 100%;
		max-width: 1200px;
		display: flex;
		flex-direction: column;
		gap: 50px;
		margin: auto;
	}
	h1 {
		text-align: center;
		margin-top: 40px;
	}

	.score {
		position: relative;
		margin-top: 20px;
		align-self: center;
		width: fit-content;
	}

	.score > span:nth-child(1) {
		display: inline-block;
		width: 50px;
	}

	.addPoint {
		position: absolute;
		top: 0;
		left: calc(100% + 10px);
		color: rgb(89, 179, 89);
		animation: add-point 0.3s ease-in-out;
	}
	@keyframes add-point {
		0% {
			opacity: 0;
		}
		100% {
			opacity: 1;
		}
	}
	.container {
		display: flex;
		justify-content: center;
		align-items: center;
		height: 500px;
	}

	.characterCardContainer {
		width: 50%;
		display: flex;
		justify-content: center;
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
		animation: buttonsPopup 0.6s ease-in-out;
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
		animation: buttonsPopup 0.6s ease-in-out;
		background-color: rgb(94, 94, 94);
	}

	.characterCard {
		position: relative;
		background-color: #303b5b;
		margin: auto;
		display: flex;
		width: 50%;
		flex-direction: column;
		gap: 10px;
		border-radius: 16px;
		width: 300px;
		min-height: 450px;
		padding-bottom: 20px;
		animation: card 0.4s ease-in-out;
	}
	.characterStatus {
		position: absolute;
		top: 10px;
		right: 10px;
		border-radius: 12px;
		box-shadow: 0px 0px 2px 1px rgba(0, 0, 0, 0.2);
		animation: buttonsPopup 0.6s ease-in-out;
		padding: 10px;
	}
	.alive {
		background-color: rgb(124, 216, 124);
	}
	.dead {
		background-color: rgb(255, 109, 109);
	}
	.unknown {
		background-color: rgb(104, 104, 104);
	}
	.loading {
		align-items: center;
		justify-content: center;
	}
	.loading > p {
		font-size: 2rem;
	}

	@keyframes card {
		0% {
			opacity: 0;
		}
		100% {
			opacity: 1;
		}
	}
	.characterCard > div {
		display: flex;
		flex-direction: column;
		justify-content: space-between;
		gap: 15px;
		padding: 0 20px;
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
	.characterCard > img {
		height: 300px;
		border-top-right-radius: 16px;
		border-top-left-radius: 16px;
		border-bottom-left-radius: 0;
		border-bottom-right-radius: 0;
	}
	.buttonContainer > .tryAgain {
		background-color: rgb(228, 129, 129);
		font-weight: 600;
		animation: buttonsPopup 0.6s ease-in-out;
	}
	@keyframes buttonsPopup {
		0% {
			opacity: 0;
		}
		100% {
			opacity: 1;
		}
	}
	.failed {
		animation: failed 0.2s forwards ease-in-out;
	}
	@keyframes failed {
		0% {
			color: white;
			/* opacity: 0; */
		}
		100% {
			color: rgb(255, 120, 120);
			/* opacity: 1; */
		}
	}
	@media only screen and (max-width: 1150px) {
		.buttonContainer > div {
			flex-direction: column;
			gap: 50px;
		}
		.buttonContainer {
			width: 30%;
		}
	}
	@media only screen and (max-width: 800px) {
		.characterCardContainer {
			width: 100%;
		}
		.container {
			flex-direction: column;
			gap: 60px;
			height: fit-content;
			min-height: 300px;
		}
		.buttonContainer > div {
			flex-direction: row;
			gap: 40px;
		}
		.buttonContainer {
			width: fit-content;
			height: 190px;
			margin-bottom: 20px;
		}
		.buttonContainer button {
			min-width: 0px;
			font-size: 1rem;
			width: 100px;
			height: 50px;
		}
	}
	@media only screen and (max-height: 850px) and (max-width: 650px) {
		.characterCardContainer {
			height: 400px;
		}
		.characterCard {
			width: 250px;
			min-height: 400px;
		}
		.characterCard img {
			height: 250px;
		}
		.characterCard > div > div:nth-child(1) {
			font-size: 1.3rem;
		}
	}
	@media only screen and (max-height: 800px) and (max-width: 600px) {
		main {
			gap: 20px;
		}
		.characterCardContainer {
			height: 400px;
		}
		.characterCard {
			width: 200px;
			min-height: 300px;
		}
		.characterCard img {
			height: 200px;
		}
		.characterCard > div > div:nth-child(1) {
			font-size: 1.3rem;
		}
	}
	@media only screen and (max-height: 750px) and (max-width: 600px) {
		.container {
			gap: 20px;
		}
		.characterCard {
			font-size: 0.8rem;
		}
		.characterCard > div > div:nth-child(1) {
			font-size: 1rem;
		}
		.buttonContainer {
			height: 180px;
			gap: 20px;
			margin-bottom: 5px;
		}
	}
</style>
