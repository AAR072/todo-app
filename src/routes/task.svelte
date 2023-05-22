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

	import "carbon-components-svelte/css/all.css";
	import {
		Checkbox,
		Button,
		TextInput,
		Theme,
		LocalStorage,
	} from "carbon-components-svelte";
	import Add from "carbon-icons-svelte/lib/Add.svelte";
	import TrashCan from "carbon-icons-svelte/lib/TrashCan.svelte";
	import Settings from "carbon-icons-svelte/lib/Settings.svelte";
	import { themeValue } from "./stores.svelte";
	let theme;
	themeValue.subscribe((value) => {
		theme = value;
	});
	let newTask;
	let tasks = [];
	let isInvalid = false;
	let events = [];

	// $: theme = toggled ? "g90" : "white";

	function ChangeCompletionStatus(index) {
		tasks[index].isCompleted = !tasks[index].isCompleted;
		tasks = [...tasks]; // Trigger reactivity
	}

	function DeleteTask(index) {
		tasks = tasks.filter((_, i) => i !== index);
	}

	function AddTask() {
		if (newTask.trim() !== "") {
			// Check if task with the same description already exists
			const existingTask = tasks.find(
				(task) => task.description === newTask
			);
			if (existingTask) {
				isInvalid = true;
				return;
			}

			tasks = [...tasks, { description: newTask, isCompleted: false }];
			newTask = "";
			isInvalid = false;
		}
	}

	function ClearCompleted() {
		tasks = tasks.filter((task) => !task.isCompleted);
	}
	function ClearUnfinished() {
		tasks = tasks.filter((task) => task.isCompleted);
	}

	function handleKeyDown(event) {
		if (event.key === "Enter") {
			event.preventDefault(); // Prevent form submission
			AddTask();
		}
	}
</script>
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
<Theme bind:theme>
	{#if isLoading}
		<div>Loading...</div>
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
		<LocalStorage
			key="tasks-storage"
			bind:value={tasks}
			on:save={() => {
				events = [...events, { event: "on:save" }];
			}}
			on:update={({ detail }) => {
				events = [...events, { event: "on:update", detail }];
			}}
		/>
		<Content>
			<Grid>
				<Row>
					<Column>
						<h2>Tasks</h2>
						<br />
						<hr />
						<br />
						<div class="flex-box">
							<TextInput
								bind:value={newTask}
								on:keydown={handleKeyDown}
								invalid={isInvalid}
								invalidText="You cannot add duplicate tasks"
								placeholder="Enter task description..."
							/>
						</div>
						<br />

						<Button on:click={AddTask} icon={Add} size="small"
							>Add task</Button
						>

						<!-- <Toggle size="sm" bind:toggled labelA="Light Mode" labelB="Dark Mode" style="margin-right: 100px"/> -->

						<br />
						<br />
						<hr />
						<br />
						<ul>
							<h3>Pending Tasks</h3>
							{#each tasks as task, index (task.description)}
								{#if !task.isCompleted}
									<li>
										<Checkbox
											style="color: {task.isCompleted
												? 'green'
												: 'red'}; display:inline-block;margin-right:30px;"
											labelText={task.description}
											checked={task.isCompleted}
											on:change={() =>
												ChangeCompletionStatus(index)}
										/>
										<Button
											size="small"
											kind="danger-tertiary"
											iconDescription="Delete"
											icon={TrashCan}
											on:click={() => DeleteTask(index)}
										/>
									</li>
								{/if}
							{/each}
							<br />
							<Button
								on:click={ClearUnfinished}
								kind="danger-tertiary"
								>Clear Pending Tasks</Button
							>
						</ul>

						<br />
						<hr />
						<br />
						<ul>
							<h3>Completed Tasks</h3>
							{#each tasks as task, index (task.description)}
								{#if task.isCompleted}
									<li>
										<Checkbox
											style="color: {task.isCompleted
												? 'green'
												: 'red'}; display:inline-block;margin-right:30px;"
											labelText={task.description}
											checked={task.isCompleted}
											on:change={() =>
												ChangeCompletionStatus(index)}
										/>
										<Button
											size="small"
											kind="danger-tertiary"
											iconDescription="Delete"
											icon={TrashCan}
											on:click={() => DeleteTask(index)}
										/>
									</li>
								{/if}
							{/each}
							<br />
							<Button
								on:click={ClearCompleted}
								kind="tertiary"
								>Clear Completed Tasks</Button
							>
							<br />
						</ul>
					</Column>
				</Row>
			</Grid>
		</Content>
	{/if}
</Theme>

<style>
	div.flex-box {
		display: flex;
		justify-content: space-between;
	}
	@import url("https://fonts.googleapis.com/css2?family=Lexend+Deca&display=swap");
	
	:global(body) {
		font-family: "Lexend Deca", sans-serif;
		font-size: 1.5rem;

	}
</style>
