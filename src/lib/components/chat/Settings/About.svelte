<script lang="ts">
	import { getVersionUpdates } from '$lib/apis';
	import { getOllamaVersion } from '$lib/apis/ollama';
	import { Falcor_BUILD_HASH, Falcor_VERSION } from '$lib/constants';
	import { Falcor_NAME, config, showChangelog } from '$lib/stores';
	import { compareVersion } from '$lib/utils';
	import { onMount, getContext } from 'svelte';
	import Tooltip from '$lib/components/common/Tooltip.svelte';
	const i18n = getContext('i18n');
	let ollamaVersion = '';
	let updateAvailable = null;
	let version = {
		current: '',
		latest: ''
	};
	const checkForVersionUpdates = async () => {
		updateAvailable = null;
		version = await getVersionUpdates(localStorage.token).catch((error) => {
			return {
				current: Falcor_VERSION,
				latest: Falcor_VERSION
			};
		});
		console.log(version);
		updateAvailable = compareVersion(version.latest, version.current);
		console.log(updateAvailable);
	};
	onMount(async () => {
		ollamaVersion = await getOllamaVersion(localStorage.token).catch((error) => {
			return '';
		});
		checkForVersionUpdates();
	});
</script>
<div class="flex flex-col h-full justify-between space-y-3 text-sm mb-6">
	<div class="space-y-3">
		<div>
			<div class="mb-2.5 text-sm font-medium flex space-x-2 items-center">
				<div>
					{$Falcor_NAME}
					{$i18n.t('Version')}
				</div>
			</div>
			<div class="flex w-full justify-between items-center">
				<div class="flex flex-col text-xs text-gray-700 dark:text-gray-200">
					<div class="flex gap-1">
						<Tooltip content={Falcor_BUILD_HASH}>
							v{Falcor_VERSION}
						</Tooltip>
						<a
							href="https://github.com/dangerpotter/falcor/releases/tag/v{version.latest}"
							target="_blank"
						>
							{updateAvailable === null
								? $i18n.t('Checking for updates...')
								: updateAvailable
									? `(v${version.latest} ${$i18n.t('available!')})`
									: $i18n.t('(latest)')}
						</a>
					</div>
					<button
						class="underline flex items-center space-x-1 text-xs text-gray-500 dark:text-gray-500"
						on:click={() => {
							showChangelog.set(true);
						}}
					>
						<div>{$i18n.t("See what's new")}</div>
					</button>
				</div>
				<button
					class="text-xs px-3 py-1.5 bg-gray-100 hover:bg-gray-200 dark:bg-gray-850 dark:hover:bg-gray-800 transition rounded-lg font-medium"
					on:click={() => {
						checkForVersionUpdates();
					}}
				>
					{$i18n.t('Check for updates')}
				</button>
			</div>
		</div>
		{#if ollamaVersion}
			<hr class="dark:border-gray-850" />
			<div>
				<div class="mb-2.5 text-sm font-medium">{$i18n.t('Ollama Version')}</div>
				<div class="flex w-full">
					<div class="flex-1 text-xs text-gray-700 dark:text-gray-200">
						{ollamaVersion ?? 'N/A'}
					</div>
				</div>
			</div>
		{/if}
		<hr class="dark:border-gray-850" />
		<a href="https://github.com/dangerpotter/falcor" target="_blank">
			<img
				alt="Github Repo"
				src="https://img.shields.io/github/stars/dangerpotter/falcor?style=social&label=Star us on Github"
			/>
		</a>
	</div>
	<div class="mt-2 text-xs text-gray-400 dark:text-gray-500">
		{#if !$Falcor_NAME.includes('Falcor')}
			<span class="text-gray-500 dark:text-gray-300 font-medium">{$Falcor_NAME}</span> -
		{/if}
		{$i18n.t('Modified from Open WebUI for Capella University by')}
		<a
			class="text-gray-500 dark:text-gray-300 font-medium"
			href="https://github.com/dangerpotter"
			target="_blank">Austin Potter</a
		>
		<p class="mt-2">
			This application utilizes a variety of open-source models to generate content, and we gratefully acknowledge the creators and licensors of these models. The following models and their licenses are used: Llama Models (Meta): Llama 3.1:latest, Llama 3.1:70b, Llama 3.2:latest, Llava:13b, all licensed under the Llama 3.1 Community License. As required by Meta Platforms, Inc., this application prominently displays "Built with Llama." Qwen Models (Alibaba): The Qwen2-math:latest and Qwen2.5:32b models are licensed under the Apache 2.0 License. Mistral Models: The Mistral-small:latest model is also licensed under the Apache 2.0 License. Gemma Models: The Gemma2:9b model is licensed under the MIT License. Phi Models: The Phi3.5:latest model is licensed under the MIT License. Nomic Models: The Nomic-embed-text:latest model is licensed under a custom open-source license. Each of these models is incorporated into the application's content generation processes in compliance with their respective licenses. We acknowledge and appreciate the open-source community's contributions and the tools made available through these licenses.
		</p>
		<p class="mt-2">
			The following licenses are included in the application's repository: Llama 3.1 Community License, Apache 2.0 License, MIT License, and Nomic Custom Open-Source License. By including the proper attributions and licensing, this app remains compliant with the respective licenses of each open-source model.
		</p>
	</div>
</div>
