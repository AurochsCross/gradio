<script lang="ts">
	import space_logo from "$lib/assets/img/spaces-logo.svg";
	import MetaTags from "$lib/components/MetaTags.svelte";
	import { page } from "$app/stores";
	import DropDown from "$lib/components/VersionDropdown.svelte";

	export let data: {
		guide: any;
		guide_slug: {
			text: string;
			href: string;
		}[];
		guide_names: {
			category: string;
			guides: {
				name: string;
				pretty_name: string;
				url: string;
			}[];
		}[];
	};
	let guide_page = data.guide;
	let guide_names = data.guide_names;
	let guide_slug = data.guide_slug;

	const COLORS = [
		"bg-green-50",
		"bg-yellow-50",
		"bg-red-50",
		"bg-pink-50",
		"bg-purple-50"
	];

	let show_all = false;

	let sidebar: HTMLElement;
	let target_link: HTMLElement;
	let navigation;
	let y: number;

	function handleAnchorClick(event: MouseEvent) {
		event.preventDefault();
		const link = event.currentTarget as HTMLAnchorElement;
		const anchorId = new URL(link.href).hash.replace("#", "");
		const anchor = document.getElementById(anchorId);
		window.scrollTo({
			top: anchor?.offsetTop,
			behavior: "smooth"
		});
	}

	$: if (sidebar) {
		if (
			target_link?.previousElementSibling?.classList.contains("category-link")
		) {
			target_link = target_link.previousElementSibling as HTMLElement;
		}
		sidebar.scrollTop = target_link?.offsetTop;
	}
	$: guide_page = data.guide;
	$: guide_slug = data.guide_slug;
</script>

<MetaTags
	title={guide_page.pretty_name}
	url={$page.url.pathname}
	canonical={$page.url.pathname}
	description="A Step-by-Step Gradio Tutorial"
/>
<div class="container mx-auto px-4 flex gap-4 relative">
	<div
		bind:this={sidebar}
		class="side-navigation h-screen leading-relaxed sticky top-0 text-md overflow-y-auto overflow-x-hidden hidden lg:block rounded-t-xl bg-gradient-to-r from-white to-gray-50"
		style="min-width: 18%"
	>	
	<div class="sticky top-0 pr-2 float-right">
		<DropDown></DropDown>
	</div>
		{#each guide_names as guides, i}
			<div
				class="category-link my-2 font-semibold px-4 pt-2 text-ellipsis block"
				style="max-width: 12rem"
			>
				{guides.category}
				{#if !show_all && i === guide_names.length - 1 && guides.category !== guide_page.category}
					<button
						class:hidden={show_all}
						class="block show-guides"
						on:click={() => (show_all = true)}
					>
						[ show ]
					</button>
				{/if}
			</div>
			{#each guides.guides as guide, j}
				{#if guide.name == guide_page.name}
					<a
						bind:this={target_link}
						class:hidden={!show_all &&
							i === guide_names.length - 1 &&
							guides.category !== guide_page.category}
						class:current-nav-link={guide.name == guide_page.name}
						class="guide-link -indent-2 ml-2 thin-link px-4 block overflow-hidden"
						style="max-width: 12rem"
						href="..{guide.url}"
						on:click={handleAnchorClick}>{guide.pretty_name}</a
					>

					<div
						class="navigation max-w-full bg-gradient-to-r from-orange-50 to-orange-100 p-2 mx-2 border-l-2 border-orange-500 mb-2"
					>
						{#each guide_slug as heading}
							<a
								class="subheading block thin-link -indent-2 ml-4 mr-2"
								href={heading.href}
								on:click={handleAnchorClick}>{heading.text}</a
							>
						{/each}
					</div>
				{:else}
					<a
						class:hidden={!show_all &&
							i === guide_names.length - 1 &&
							guides.category !== guide_page.category}
						class:current-nav-link={guide.name == guide_page.name}
						class="guide-link -indent-2 ml-2 thin-link px-4 block overflow-hidden"
						style="max-width: 12rem"
						href="..{guide.url}">{guide.pretty_name}</a
					>
				{/if}
			{/each}
		{/each}
	</div>
	<div class="w-10/12 mx-auto">
		{#if guide_page.spaces.length}
			<div id="spaces-holder" class="mb-4">
				<a href="https://hf.co/spaces" target="_blank">
					<img
						class="inline-block my-0 mx-auto w-5 max-w-full pb-1"
						src={space_logo}
					/>
				</a>
				<p class="m-0 inline text-lg font-normal">Related Spaces:</p>
				{#each guide_page.spaces as space, i}
					<div
						class="space-link inline-block m-1 px-1 rounded-md {COLORS[i] ??
							COLORS[i - COLORS.length]}"
					>
						<a href={space} target="_blank" class="no-underline"
							>{space.slice(30)}</a
						>
					</div>
				{/each}
			</div>
		{/if}
		<div class="prose text-lg max-w-full">
			{@html guide_page.new_html}
		</div>
	</div>
</div>

<svelte:window bind:scrollY={y} />
