<script>
	import { browser } from '$app/environment';
	import { onMount } from 'svelte';

	let todos = [];
	let newTodoText = '';

	onMount(() => {
		if (browser) {
			todos = JSON.parse(localStorage.getItem('todos') || '[]');
		}
	});

	function addTodo(e) {
		e.preventDefault();

		if (newTodoText.trim()) {
			todos = [
				...todos,
				{
					id: Date.now(),
					text: newTodoText,
					completed: false
				}
			];
			newTodoText = '';
		}
	}

	$: if (browser) {
		localStorage.setItem('todos', JSON.stringify(todos));
		console.log('Todos saved', todos);
	}

	function toggleTodo(id) {
		todos = todos.map((todo) => {
			if (todo.id === id) {
				return { ...todo, completed: !todo.completed };
			}
			return todo;
		});
	}
</script>

<main>
	<h1>Todo List</h1>

	<form on:submit={addTodo}>
		<input type="text" bind:value={newTodoText} placeholder="Add new todo..." />
		<button type="submit">Add todo</button>
	</form>

	<ul>
		{#each todos as todo (todo.id)}
			<li class="todo-item">
				<label class:completed={todo.completed}
					>{todo.completed}
					<input
						type="checkbox"
						checked={todo.completed}
						on:change={() => toggleTodo(todo.id)}
					/></label
				>
				<span>{todo.text}</span>
			</li>
		{:else}
			<li class="empty-message">No todos</li>
		{/each}
	</ul>
</main>

<style>
	main {
		max-width: 600px;
		margin: 20px auto;
		padding: 20px;
	}

	form {
		display: flex;
		gap: 10px;
		border: 1px solid #ddd;
		border-radius: 4px;
	}

	input {
		flex: 1;
		padding: 8px;
		border: 1px solid #ddd;
		border-radius: 4px;
	}

	.todo-item {
		padding: 8px;
		margin: 4px 0;
		background: #f9f9f9;
		border-radius: 4px;
	}

	.empty-message {
		text-align: center;
		color: #888;
		padding: 20px;
	}

	.completed span {
		text-decoration: line-through;
		color: #888;
	}

	label {
		display: flex;
		align-items: center;
		gap: 8px;
	}

	input[type='checkbox'] {
		width: 18px;
		height: 18px;
	}
</style>
