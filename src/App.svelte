<script>
	import CountdownTimer from './CountdownTimer.svelte'
	import { timers } from './stores.js'
	import { v4 as uuidv4 } from 'uuid'

	timers.useLocalStorage()

	let name = ''
	let date = null
	let time = null
	let validated = false
	let errors = []
	let showForm = false

	function deleteTimer() {
        let index = $timers.findIndex(element => {
            element.id === timer.id
        })

        $timers.splice(index, 1)

		timers.set($timers)
    }

	function handleClick(e) {

		validated = true

		errors = []

		if (name.length === 0) {
			errors.push('You must provide an event name')
		}
		if (!date) {
			errors.push('You must provide a date')
		}

		date = `${date} ${time || ''}`
		if (new Date(date) < new Date()) {
			errors.push('The specified date and time has already occurred')
		}

		if (errors.length === 0) {
			showForm = false
			$timers.push({
				id: uuidv4(),
				event: name,
				date: date
			})

			timers.set($timers)

			validated = false
		}
	}

</script>

<div class="uk-container">

	<div>
		{#if showForm}
			<div class="uk-align-center uk-width-1-3 uk-background-default">
				<form autocomplete="off" class="uk-padding">
					<label for="event" class="uk-text-bold">Event name</label>
					<input type="text" name="name" id="name" bind:value={name}
						on:input={(e) => { validated = false }}
						class="uk-input uk-margin-bottom"
						class:warning="{validated && name.length === 0}">
					
					<label for="date" class="uk-text-bold">Date</label>
					<input type="date" id="date" name="date" bind:value={date}
						on:input={(e) => { validated = false }}
						class="uk-input uk-margin-bottom"
						class:warning="{validated && !date}">
					
					<label for="time" class="uk-text-bold">Time</label>
					<input type="time" id="time" name="time" bind:value={time} class="uk-input uk-margin-bottom">
					<button on:click|preventDefault={handleClick} class="uk-button uk-button-primary">Create</button>
					<button on:click={(e) => showForm = false } class="uk-button uk-button-default">Cancel</button>
				</form>

				{#if errors.length > 0}
					<div class="uk-padding">
						{#each errors as error}
							<p class="error">{error}</p>
						{/each}
					</div>
				{/if}
			</div>
		{:else}
			<button type="button" class="uk-button uk-button-default uk-button-large uk-align-center timer" on:click={(e) => {
				name = ''
				date = ''
				showForm = true
			}}>
				Add new timer<span uk-icon="icon: arrow-right"></span>
			</button>
		{/if}
	</div>

	<div class="uk-grid">
		{#each $timers as timer}
			<CountdownTimer timer={timer} />
		{/each}
	</div>
</div>

<style>
	.timer {
		color: white;
	}

	.warning {
		border: 2px solid red;
	}
	.error {
		color: red;
	}
</style>