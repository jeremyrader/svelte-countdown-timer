<script>

    import { timers } from './stores.js'
    export let timer

    let countdown = '00 : 00: 00 : 00'

    let date1 = new Date()
    let date2 = new Date(timer.date)
  
    if (date2 > date1) {
        let interval = setInterval(() => { 
            
            date1 = new Date()

            const DAY = 1000 * 60 * 60 * 24
            const HOUR = 1000 * 60 * 60
            const MIN = 1000 * 60
            const MS = 1000

            let diffTime = Math.abs(date2 - date1)

            let days = Math.floor(diffTime / DAY)

            let daysinms = days * DAY
            let hours = Math.floor((diffTime - daysinms) / HOUR)

            let hoursinms = hours * HOUR
            let mins = Math.floor((diffTime - daysinms - hoursinms) / MIN)

            let mininms = mins * MIN
            let sec = Math.floor((diffTime - daysinms - hoursinms - mininms) / MS)

            countdown = `${pad(days)} : ${pad(hours)} : ${pad(mins)} : ${pad(sec)}`

            if (date2 - date1 < 0) {
                clearInterval(interval)
                alert(timer.event)
            }
        }, 100);
    }

    function pad(number) {
        return String(number).padStart(2, '0')
    }

    function deleteTimer() {
        let index = $timers.findIndex(element => element.id === timer.id)
        $timers.splice(index, 1)

		timers.set($timers)
    }

</script>

<div class:uk-text-danger="{date2 - date1 < 0}" class="uk-card uk-card-default uk-card-body uk-margin-right uk-margin-bottom uk-text-center uk-width-1-5">
    <button type="button" uk-close class="uk-align-right" on:click={deleteTimer}></button>
    <p class:uk-text-danger="{date2 - date1 < 0}" class="uk-card-title">{timer.event}</p>
    <p>{countdown}</p>
</div>