<script>
	import ListChecked from "carbon-icons-svelte/lib/ListChecked.svelte";
	import {
		Checkbox,
		Button,
		Theme,
		Toggle,
		LocalStorage,
	} from "carbon-components-svelte";
	import "carbon-components-svelte/css/all.css";


    import {themeValue} from "../stores.svelte"

	let toggled;
	let events = [];
    let theme;
    // $: theme = toggled ? "g90" : "white"
	$: themeValue.set(toggled ? "g90" : "white");

themeValue.subscribe((value) => {
		theme = value;
	});


	
</script>
<Theme theme={theme}>

<title>Settings</title>
<h2>Settings</h2>
<br />
<br />
<hr />
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
<Toggle bind:toggled labelA="Light Mode" labelB="Dark Mode" />
</Theme>