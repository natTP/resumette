<script lang="ts">
	import { onMount } from 'svelte';
	import type { IProfileResp } from '../types';
	import Hideable from './Hideable.svelte';
	import Intro from './Intro.svelte';
	import Kofi from './Kofi.svelte';
	import Work from './Work.svelte';

	let profile: IProfileResp;

	$: dataLink = `${sourceLink}/blob/main/static/data/profile.json`;
	$: ({
		intro = {} as IProfileResp['intro'],
		projects = [],
		technologies = [],
		workExperiences = [],
		educations = [],
		activities = [],
		resumeUrl: { sourceLink = '', fullVersionLink = '' } = {}
	} = profile || {});

	onMount(async () => (profile = await fetchResumeProfile()));

	async function fetchResumeProfile() {
		const resp = await fetch('/data/profile.json');
		return await resp.json();
	}
</script>

<!-- Remove this is you does not want Kofi widget on your site -->
<!-- {#if intro.github == 'narze'}
	<Kofi name={intro.github} />
{/if} -->

<header class="web-only text-center p-4 sm:p-6 bg-purple-400 text-white w-screen font-9">
	<h1 class="text-4xl">Resumette</h1>
	<h3>
		<button on:click={() => window.print()} class="underline text-lg">[Print]</button>
	</h3>
	<p>
		Printer-friendly standard résumé, any HTML tags with <code>web-only</code> CSS class will be hidden
		on print.
	</p>
	<p>You can click at any sections or lines hide some information before printing.</p>
	<a href={sourceLink} target="_blank" rel="noopener">[Source]</a>
	<a href={dataLink} target="_blank" rel="noopener">[Data]</a>
</header>

<main class="text-center p-4 m-0 md:m-8 xl:mx-auto max-w-screen-xl font-9">
	<Intro {...intro} />

	<div class="md:grid print:grid grid-cols-3 gap-x-6">
		<aside class="col-span-1">
			<section>
				<Hideable>
					<h2 class="text-2xl print:text-4xl uppercase text-left">Education</h2>
					<hr />
		
					<ul class="text-left list-disc pl-8">
						{#each educations as edu}
							<Hideable>
								<li class="mb-2">
									<strong>{edu.head}</strong>, {edu.details}
								</li>
							</Hideable>
						{/each}
					</ul>
				</Hideable>
			</section>
			<section>
				<Hideable>
					<h2 class="text-2xl print:text-4xl uppercase text-left">Skills and Technologies</h2>
					<hr />
					<ul class="text-left list-disc pl-8">
						{#each technologies as tech}
							<Hideable>
								<li class="mb-4">
									<span class="font-bold">{tech.section}</span>
									<ul>
										{#each tech.details as item}
											<li>
												{item}
											</li>
										{/each}
									</ul>
								</li>
							</Hideable>
						{/each}
					</ul>
				</Hideable>
			</section>
		</aside>
	
		<div class="col-span-2">
			<section>
				<Hideable>
					<h2 class="text-2xl print:text-4xl uppercase text-left">Work Experience</h2>
					<hr />
		
					{#each workExperiences as exp}
						<Work {...exp} />
					{/each}
				</Hideable>
			</section>
		
			<section>
				<Hideable>
					<h2 class="text-2xl print:text-4xl uppercase text-left">Projects</h2>
					<hr />
		
					<ul class="text-left list-disc pl-8">
						{#each projects as project}
							<Hideable hide={project.hide}>
								<li class="mb-2">
									<strong>{project.name}</strong>
									- {project.details}
									<em>
										{project.role}
									</em>
									<a href="https://{project.url}" target="_blank" rel="noreferrer" class="ml-2"
										><strong>{project.url}</strong></a
									> 
								</li>
							</Hideable>
						{/each}
					</ul>
				</Hideable>
			</section>
		</div>
		
		<section class="col-span-full">
			<Hideable>
				<h2 class="text-2xl print:text-4xl uppercase text-left">Extracurricular Activities</h2>
				<hr />
	
				<ul class="text-left list-disc pl-8">
					{#each activities as activity}
						<Hideable hide={activity.hide}>
							<li class="mb-2">
								<div class="flex ">
									<strong class="text-left">{activity.name}</strong>
									<a href="https://{activity.url}" target="_blank" rel="noreferrer"
										><strong>{activity.url}</strong></a
									>
									<div class="flex-1 text-right font-bold">
										{activity.date.join('-')}
									</div>
								</div>
								{activity.details}
							</li>
						</Hideable>
					{/each}
				</ul>
			</Hideable>
		</section>
	</div>

	<footer class="print-only">
		(See <a href={fullVersionLink} target="_blank" rel="noopener">full version</a>
		or <a href={sourceLink} target="_blank" rel="noopener">source</a>)
	</footer>
</main>

<style lang="postcss">
	main {
		overflow-x: hidden;
	}

	a {
		text-decoration: underline;
	}

	section {
		@apply my-4;
	}

	section h2 {
		@apply font-bold;
	}

	section hr {
		@apply mt-0 mb-2;
		border-color: darkgrey;
	}

	:global(.print-only) {
		display: none;
	}

	@media print {
		* {
			@apply text-xs;
		}

		:global(.print-only) {
			display: inherit;
		}

		:global(.web-only) {
			display: none;
		}

		ul {
			@apply pl-6;
		}

		section {
			@apply my-2;
		}

		section hr {
			@apply mt-0 mb-1;
		}


		main {
			margin: 0 0;
			padding: 0;
		}
	
	}
</style>
