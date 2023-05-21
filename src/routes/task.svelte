<script>
	import "carbon-components-svelte/css/g90.css";
	import { Checkbox, Button, TextInput } from "carbon-components-svelte";
	import Add from "carbon-icons-svelte/lib/Add.svelte";
	import TrashCan from "carbon-icons-svelte/lib/TrashCan.svelte";

	export let newTask;
	export let tasks = [];
	let isInvalid = false;

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
	function handleKeyDown(event) {
		if (event.key === "Enter") {
			event.preventDefault(); // Prevent form submission
			AddTask();
		}
	}
</script>

<TextInput
	bind:value={newTask}
	on:keydown={handleKeyDown}
	invalid={isInvalid}
	invalidText="You cannot add duplicate tasks"
	placeholder="Enter task description..."
/><br />
<Button on:click={AddTask} icon={Add} size="small">Add task</Button>
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
</ul>

<style>
</style>
