<script>
	import supabase from '$lib/db'
	import { user } from '$lib/stores'
	import { goto } from '$app/navigation'

	let email = ''
	let isNewRegistration = false

	const signUp = async () => {
		let { user: userDetails, error } = await supabase.auth.signUp({
			email: email,
			password: 'iSJiDnprOYrblUaXnsDP'
		})
		$user = userDetails
		goto('/')
	}

	const logIn = async () => {
		let { user: userDetails, error } = await supabase.auth.signIn({
			email: email,
			password: 'iSJiDnprOYrblUaXnsDP'
		})
		$user = userDetails
		goto('/')
	}
</script>

<!-- in this case, only email -->
<label for="">
	Email:
	<input bind:value="{email}" type="email" placeholder="email@email.com" />
</label>
<br />
<br />
{#if isNewRegistration}
	<button on:click="{signUp}">SignUp</button>
	<button class="switch" on:click="{() => (isNewRegistration = false)}"
		>Already have an account?</button
	>
{:else}
	<button on:click="{logIn}">Login</button>
	<button class="switch" on:click="{() => (isNewRegistration = true)}">Create a new account?</button
	>
{/if}
