<script>
import { onMount } from "svelte";

let titles = ['#', "Coin", "Price", "Price Change", "24h Volume"]
let coins = []
let filteredCoins = []
let inputRef = null

//Consume CoinGecko API
const loadCoins = async () => {
	const res = await fetch('https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=100&page=1&sparkline=false');
	const data = await res.json()
	console.log(data);
	coins = data
	filteredCoins = data
}

loadCoins();

const handleSearch = (value) => {
	filteredCoins = coins.filter((coin) => 
	coin.name.toLowerCase().includes(value.toLowerCase()) ||
	coin.symbol.toLowerCase().includes(value.toLowerCase())
	);
};

onMount(() => {
	inputRef.focus()
});

</script>

<div class="container">
	<div class="row">
		<h1>CryptoCoin Search App</h1>
		<p class="text-muted">Made by <a href="https://github.com/rocampocastro604/crypto-search">rocampocastro604</a></p>
		<input 
		type="text" 
		class="form-control bg-dark text-light my-4"
		placeholder="Search a coin..."
		on:keyup={({target: {value}}) => handleSearch(value)}
		bind:this={inputRef}>

		<table class="table table-dark table-striped">
			<thead>
				<tr>
					{#each titles as title}
						<th>{title}</th>						
					{/each}
				</tr>
			</thead>
			<tbody>
				{#each filteredCoins as coin, id}
				<tr>
					<td class="text-muted">{id + 1}</td>
					<td>
						<img src={coin.image} alt={coin.name} style="width: 2rem;" class="img-fluid me-2">
						<span>
						{coin.name}		
						</span>
						<span class="text-muted text-uppercase ms-2">
							{coin.symbol}		
						</span>
					</td>
					<td>${coin.current_price.toLocaleString()}</td>
					<td class={coin.price_change_percentage_24h > 0 ? "text-success" : "text-danger"}>
							{coin.price_change_percentage_24h.toLocaleString()}%
					</td>
					<td>${coin.total_volume.toLocaleString()}</td>
				</tr>
				{/each}
			</tbody>
		</table>
	</div>
</div>

<style>
h1 {
	text-align: center;
	margin-top: 2rem;
}

p { 
	text-align: center;
}
</style>