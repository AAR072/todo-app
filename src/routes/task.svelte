<script>
	import { writable } from "svelte/store";
	import "carbon-components-svelte/css/all.css";
	import {
		Checkbox,
		Button,
		TextInput,
		Theme,
		Toggle,
		LocalStorage,
	} from "carbon-components-svelte";
	import Add from "carbon-icons-svelte/lib/Add.svelte";
	import TrashCan from "carbon-icons-svelte/lib/TrashCan.svelte";

	let theme = "g90";
	let newTask;
	let tasks = [];
	let isInvalid = false;
	let toggled = false;
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

	function ThemeSwitch() {
		if (theme == "g90") {
			theme = "white";
		} else {
			theme = "g90";
		}
		console.log("here");
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

<Theme bind:theme>
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

	<TextInput
		bind:value={newTask}
		on:keydown={handleKeyDown}
		invalid={isInvalid}
		invalidText="You cannot add duplicate tasks"
		placeholder="Enter task description..."
		style="width: 600px"
	/>
	<br />

	<Button on:click={AddTask} icon={Add} size="small">Add task</Button>

	<!-- <Toggle size="sm" bind:toggled labelA="Light Mode" labelB="Dark Mode" style="margin-right: 100px"/> -->

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
						on:change={() => ChangeCompletionStatus(index)}
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
		<Button on:click={ClearUnfinished} kind="danger-tertiary"
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
						on:change={() => ChangeCompletionStatus(index)}
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
		<br /><br /><br />
		<Button on:click={ClearCompleted} kind="danger-tertiary"
			>Clear Completed Tasks</Button
		>
		<br />
	</ul>
</Theme>
