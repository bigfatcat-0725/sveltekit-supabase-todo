<script>
	import supabase from '$lib/db'
	import { user } from '$lib/stores'
	import { onMount } from 'svelte'
	import { goto } from '$app/navigation'
	import Todo from '$lib/components/Todo.svelte'

	let todos = []
	let newTask = ''

	onMount(async () => {
		await getAllTodos()
	})

	const getAllTodos = async () => {
		try {
			let { data, error } = await supabase.from('todos').select('*')
			todos = data
		} catch (err) {
			console.log(err)
		}
	}

	const addNewTodo = async () => {
		try {
			const { data, error } = await supabase
				.from('todos')
				.insert([{ task: newTask, user_id: $user.id }])
			await getAllTodos()
			newTask = ''
		} catch (err) {
			console.log(err)
		}
	}

	const updateTodo = async (todo) => {
		try {
			const { data, error } = await supabase
				.from('todos')
				.update({ task: todo.task, isComplete: todo.isComplete })
				.eq('id', todo.id)
			await getAllTodos()
		} catch (err) {
			console.log(err)
		}
	}

	const deleteTodo = async (todo) => {
		try {
			const { data, error } = await supabase.from('todos').delete().eq('id', todo.id)
			await getAllTodos()
		} catch (err) {
			console.log(err)
		}
	}

	const handlekeyPress = (event) => {
		if (event.key === 'Enter' && newTask !== '') {
			addNewTodo()
		}
	}

	const logOut = async () => {
		let { error } = await supabase.auth.signOut()
		$user = false
		goto('/login')
	}
</script>

{#if !$user.email}
	<button on:click="{() => goto('/login')}"> please login </button>
{/if}

<h4>Welcome {$user?.email ? $user.email : ''}!</h4>

<div class="add-todo">
	<input type="text" bind:value="{newTask}" />
	<button on:click="{addNewTodo}">Add Task</button>
</div>

{#each todos as todo}
	<Todo todo="{todo}" updateTodo="{updateTodo}" deleteTodo="{deleteTodo}" />
{:else}
	<p>No todos found</p>
{/each}

{#if $user.email}
	<p on:click="{logOut}" class="switch">Logout</p>
{/if}

<svelte:window on:keypress="{handlekeyPress}" />

<style>
	.add-todo {
		display: flex;
		margin-bottom: 1rem;
	}
	:global(.switch) {
		color: lightskyblue;
		cursor: pointer;
		border: none;
		background-color: transparent;
	}
	:global(.switch:hover) {
		text-decoration: underline;
	}
</style>
