<script>
	import { Theme, Toggle, LocalStorage } from "carbon-components-svelte";
	import "carbon-components-svelte/css/all.css";

	import { themeValue } from "../stores.svelte";
	import {
		Header,
		SideNav,
		SideNavItems,
		SideNavLink,
		SkipToContent,
		Content,
		Grid,
		Row,
		Column, Button
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

	let toggled;
	let events = [];
	let theme;
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
						<h2>Settings</h2>
						<br />
						<hr />
						<Toggle
							bind:toggled
							labelA="Light Mode"
							labelB="Dark Mode"
						/>
						<br>
						
					</Column>
				</Row>
			</Grid>
		</Content>
		<title>Settings</title>

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
