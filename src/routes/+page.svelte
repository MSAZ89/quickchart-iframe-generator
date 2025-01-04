<script lang="ts">
	let chartType = $state('bar');
	let labels = $state(['Q1', 'Q2', 'Q3', 'Q4']);
	let datasets = $state([
		{ label: 'Users', data: [50, 60, 70, 180] },
		{ label: 'Revenue', data: [100, 200, 300, 400] }
	]);

	function addLabel() {
		labels = [...labels, `Label ${labels.length + 1}`];
		datasets = datasets.map((dataset) => ({
			...dataset,
			data: [...dataset.data, 0]
		}));
	}

	function removeLabel(index: number) {
		labels = labels.filter((_, i) => i !== index);
		datasets = datasets.map((dataset) => ({
			...dataset,
			data: dataset.data.filter((_, i) => i !== index)
		}));
	}

	function addDataset() {
		datasets = [
			...datasets,
			{
				label: `Dataset ${datasets.length + 1}`,
				data: Array(labels.length).fill(0)
			}
		];
	}

	function removeDataset(index: number) {
		datasets = datasets.filter((_, i) => i !== index);
	}

	const chartUrl = $derived(
		`https://quickchart.io/chart?c=${encodeURIComponent(
			JSON.stringify({
				type: chartType,
				data: {
					labels: labels,
					datasets: datasets
				}
			})
		)}`
	);
</script>

<div class="container mx-auto grid grid-cols-1 gap-4 p-4 md:grid-cols-[300px_1fr]">
	<div class="space-y-4">
		<select bind:value={chartType} class="w-full rounded border p-2">
			<option value="bar">Bar</option>
			<option value="line">Line</option>
			<option value="pie">Pie</option>
			<option value="radar">Radar</option>
			<option value="polarArea">Polar Area</option>
			<option value="radialGauge">Radial Gauge</option>
		</select>

		<div class="space-y-2">
			<h3 class="font-bold">Labels</h3>
			{#each labels as label, i}
				<div class="flex gap-2">
					<input bind:value={labels[i]} class="flex-1 rounded border p-2" />
					<button
						onclick={() => removeLabel(i)}
						class="rounded bg-red-500 px-3 py-2 text-white hover:bg-red-600">×</button
					>
				</div>
			{/each}
			<button
				onclick={addLabel}
				class="w-full rounded bg-blue-500 p-2 text-white hover:bg-blue-600"
			>
				Add Label
			</button>
		</div>

		<div class="space-y-2">
			<h3 class="font-bold">Datasets</h3>
			{#each datasets as dataset, datasetIndex}
				<div class="space-y-2 rounded border p-3">
					<div class="flex gap-2">
						<input bind:value={datasets[datasetIndex].label} class="flex-1 rounded border p-2" />
						<button
							onclick={() => removeDataset(datasetIndex)}
							class="rounded bg-red-500 px-3 py-2 text-white hover:bg-red-600">×</button
						>
					</div>
					<div class="grid grid-cols-2 gap-2">
						{#each dataset.data as value, valueIndex}
							<input
								type="number"
								bind:value={datasets[datasetIndex].data[valueIndex]}
								class="w-full rounded border p-2"
							/>
						{/each}
					</div>
				</div>
			{/each}
			<button
				onclick={addDataset}
				class="w-full rounded bg-blue-500 p-2 text-white hover:bg-blue-600"
			>
				Add Dataset
			</button>
		</div>
		<div class="max-w-full overflow-x-auto">
			{chartUrl}
			<button
				class="rounded bg-blue-500 p-2 py-0 text-white"
				onclick={() =>
					navigator.clipboard.writeText(
						`<iframe title="chart" src="${chartUrl}" style="height: 100vh; width: 100%;" frameborder="0"></iframe>`
					)}>Generate Iframe</button
			>
		</div>
	</div>

	<div class="rounded-lg bg-white p-4 shadow">
		<iframe title="chart" src={chartUrl} class="h-screen w-full" frameborder="0"></iframe>
	</div>
</div>
