<script>
    import { onMount, onDestroy } from 'svelte';
    import { Grid, Icon } from 'stdf';
    import { weather } from '@/store';
    import { weather as weatherApp } from '$lib/appConfig';
    import { appConfig } from '@/store';
    export let row = 3;
    export let col = 1;
    export let injClass = '';
    let weatherIcons = {'晴': 'ri-sun-line','多云': 'ri-sun-cloudy-line', '阴': 'ri-cloud-line', '雨': 'ri-rainy-line'};
    let timer = [];
    onMount(() => {
        timer.map(el => el && clearInterval(el))
        timer.push(setInterval(() => {
            weather.refresh()
        }, 60000))
    })
    onDestroy(() => {
        timer.map(el => clearInterval(el))
    })
</script>
<Grid {row} {col}>
    <a
        on:click={(e) => {e.stopPropagation();$appConfig.app = weatherApp;}}
        href="/micro/{weatherApp.url}/{weatherApp.text}/{weatherApp.icon}"
        class="py-6 h-full {injClass} dark:bg-black h-full rounded-xl text-xs text-center flex flex-col justify-around items-center shadow dark:shadow-white/10"
    >
        <div class="location text-xl">{$weather.location?.name||'深圳'}</div>
        <Icon name="{weatherIcons[$weather.now?.text]||'ri-sun-line'}" size={40} injClass="py-2" />
        <span class="text-xl">{$weather.now?.temperature||25}℃</span>
    </a>
</Grid>