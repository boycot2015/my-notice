<svelte:head>
    <title>{title}</title>
</svelte:head>
<style lang="less" scoped>
    // .layout {
    //     padding-bottom: var(--gap-60);
    // }
:global(.bottom-tab-bar) {
    position: fixed !important;
    bottom: 0;
    width: 100%;
    z-index: 999;
    background-color: var(--color-fff);
}
</style>
<script>
    // /** @type {import('./$types').LayoutData} */
	// export let data;
    import { TabBar, Input } from 'stdf';
    import Action from '$lib/components/Action.svelte';
    import { scale } from 'svelte/transition';
    import { quintOut } from 'svelte/easing';
    const labels = [
		{ text: '首页', icon: { name: 'ri-home-3-line', size: 20 }, activeIcon: { name: 'ri-home-3-fill', size: 20 } },
		{
			text: '发现',
			icon: { name: 'ri-compass-3-line', size: 20 },
			activeIcon: { name: 'ri-compass-3-fill', size: 20 },
		},
		{
			text: '消息',
			icon: { name: 'ri-discuss-line', size: 20 },
			activeIcon: { name: 'ri-discuss-fill', size: 20 },
		},
		{
			text: '我的',
			icon: { name: 'ri-account-circle-line', size: 20 },
			activeIcon: { name: 'ri-account-circle-fill', size: 20 },
		},
	];
    let title = '品牌推荐'
    let offsetHeight = 0;
</script>
<div transition:scale="{{ duration: 500, opacity: 0.3, start: 0.3, easing: quintOut }}" class="layout" style="padding-top: {offsetHeight}px; background: linear-gradient(190deg, rgb(181, 121, 242), rgb(242, 121, 131));">
    <Action bind:offsetHeight={offsetHeight} path="/" {title} />
    <slot></slot>
    <TabBar injClass="bottom-tab-bar" love {labels} />
</div>