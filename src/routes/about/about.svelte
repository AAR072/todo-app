<script>
	import SvelteMarkdown from "svelte-markdown";
	import {
		Header,
		SideNav,
		SideNavItems,
		SideNavLink,
		SkipToContent,
		Content,
		Grid,
		Row,
		Column,
	} from "carbon-components-svelte";
	import { Loading } from "carbon-components-svelte";
	import { onMount } from "svelte";
	let isLoading = true;

	onMount(() => {
		if (document.readyState === "complete") {
			isLoading = false;
			console.log("Page loaded");
		} else {
			window.addEventListener("load", () => {
				isLoading = false;
				console.log("Page loaded");
			});
		}
	});

	let isSideNavOpen = false;
	const source = `
# TaskApp
<hr>
Task app is an easy to use app to quickly set goals for yourself. Made in <a target=”_blank” href="https://svelte.dev">Svelte<a/>

<br>
<br>
Github repo: <a target=”_blank” href="https://github.com/AAR072/todo-app/">AAR072/todo-app<a/>
<br>
<br>

## Feedback
<hr>
If you have any feedback, create an issue at https://github.com/AAR072/todo-app/issues

<br>
<br>

## Acknowledgements
<hr>
<br>

- [AAR072](https://github.com/AAR072)
- [Svelte documentation](https://svelte.dev/docs)
- [Carbon Design System](https://carbondesignsystem.com/)
- [Design Inspiration](https://vocalize.cloud/)

`;
	import {
		Theme,
		LocalStorage,
	} from "carbon-components-svelte";
	import "carbon-components-svelte/css/all.css";

	import { themeValue } from "../stores.svelte";

	let toggled;
	let events = [];
	let theme;
	// $: theme = toggled ? "g90" : "white"
	$: themeValue.set(toggled ? "g100" : "white");

	themeValue.subscribe((value) => {
		theme = value;
	});
</script>

<Theme {theme}>
	{#if isLoading}
		<Loading description="Loading..." />
	{:else}
		<Header
			persistentHamburgerMenu={true}
			platformName="💀 TaskApp"
			bind:isSideNavOpen
		>
			<svelte:fragment slot="skip-to-content">
				<SkipToContent />
			</svelte:fragment>
		</Header>

		<SideNav bind:isOpen={isSideNavOpen}>
			<SideNavItems>
				<SideNavLink text="Tasks" href="/" />
				<SideNavLink text="Settings" href="/settings" />
				<SideNavLink text="About" href="/about" />
			</SideNavItems>
		</SideNav>
		<Content>
			<Grid>
				<Row>
					<Column>
						<SvelteMarkdown {source} />
					</Column>
				</Row>
			</Grid>
		</Content>
		<title>About</title>

		<LocalStorage
			key="current-theme"
			bind:value={toggled}
			on:save={() => {
				events = [...events, { event: "on:save" }];
			}}
			on:update={({ detail }) => {
				events = [...events, { event: "on:update", detail }];
			}}
		/>
		<LocalStorage
			key="theme-value"
			bind:value={theme}
			on:save={() => {
				events = [...events, { event: "on:save" }];
			}}
			on:update={({ detail }) => {
				events = [...events, { event: "on:update", detail }];
			}}
		/>
	{/if}
</Theme>

<style>
	@import url("https://fonts.googleapis.com/css2?family=Lexend+Deca&display=swap");

	:global(body) {
		font-family: "Lexend Deca", sans-serif;
	}
</style>
