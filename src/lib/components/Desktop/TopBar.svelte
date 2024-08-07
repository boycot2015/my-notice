<script lang="ts">
    import { appConfig } from '@/store';
    import 'dayjs/locale/zh-cn.js';
    import dayjs from 'dayjs';
    import Icon from '$lib/components/Icon.svelte';
    import Weather from '$lib/components/apps/weather.svelte';
    import { onMount, onDestroy } from 'svelte';
    import { sineInOut } from 'svelte/easing';
    import { tweened } from 'svelte/motion';
    import { fadeIn } from '$lib/helpers/fade';
    import { settings } from '$lib/appConfig';
    import type { AppID } from '@/store/apps.store';
    import Calendar from '$lib/components/apps/Calendar.svelte';
    import { createAppConfig } from '$lib/helpers/create-app-config';
    import {
        activeApp,
        openApps,
    } from '@/store/apps.store';
    
    let appID: AppID;
    let timer = null;
    let timeStr = '';
    let locale = 'zh-cn';
    $: dayjs.locale(locale);
    // Spring animation for the click animation
    const appOpenIconBounceTransform = tweened(0, {
        duration: 400,
        easing: sineInOut,
    });

    async function bounceEffect() {
        // Animate the icon
        await appOpenIconBounceTransform.set(-40);

        // Now animate it back to its place
        appOpenIconBounceTransform.set(0);
    }

    async function openApp(e, app) {
        appID = app.id || app.text || 'micro'
        $openApps[appID] = createAppConfig({
            ...app
        });
        const { shouldOpenWindow, externalAction } = $openApps[appID] || { shouldOpenWindow: true };
        if (!shouldOpenWindow) return externalAction?.(e);
        // For the bounce animation
        const isAppAlreadyOpen = !!$openApps[appID];
        
        $activeApp = appID;
        if (isAppAlreadyOpen) return;
        bounceEffect();
    }
    const timeFormat = (time, split = '/') => {
        return new Date(time).toLocaleString().split('/').join(split)
    }
    onMount(async () => {
        clearInterval(timer)
        timeStr = timeFormat(new Date())
        timer = setInterval(() => timeStr = timeFormat(new Date()), 1000)
    });
    onDestroy(() => {
        clearInterval(timer)
    })
  </script>
  
  <!-- svelte-ignore a11y-no-static-element-interactions -->
  <div in:fadeIn={{ duration: 500 }} class="top-bar" on:dblclick|stopPropagation>
    <div class="menu">
        <Icon name="ri-{$appConfig.isWin?'windows':'apple'}-fill" size={22} on:click={(e) => openApp(e, settings)} />
    </div>
    <div class="info flex align-c">
        <Weather size={22} injClass="w-[8rem] mx-4 h-[2rem] flex flex-row justify-between items-center" />
        <Calendar />
        <!-- <Icon name="ri-information-fill" size={22} on:click={(e) => openApp(e, settings)} /> -->
    </div>
  </div>
  
  <style lang="less">
    .top-bar {
        position: absolute;
        top: 0;
        left: 0;
        height: 34px;
        width: 100vw;
        padding: 0 30px;
        display: flex;
        z-index: 99;
        justify-content: space-between;
        align-items: center;
        color: var(--color-fff);
        background-color: rgba(0, 0, 0, 0.6);
    }
  </style>
  