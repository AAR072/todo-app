<script>
	import {
		Header,
		HeaderNav,
		HeaderNavItem,
		HeaderNavMenu,
		SideNav,
		SideNavItems,
		SideNavMenu,
		SideNavMenuItem,
		SideNavLink,
		SkipToContent,
		Content,
		Grid,
		Row,
		Column,
	} from "carbon-components-svelte";

	let isSideNavOpen = false;

	import ListChecked from "carbon-icons-svelte/lib/ListChecked.svelte";
	import {
		Checkbox,
		Button,
		Theme,
		Toggle,
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
</Theme>

<style>
	div.flex-box {
		display: flex;
		justify-content: space-between;
	}
	@import url("https://fonts.googleapis.com/css2?family=Lexend+Deca&display=swap");

	:global(body) {
		font-family: "Lexend Deca", sans-serif;
	}
</style>
