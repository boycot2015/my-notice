<style lang="less" scoped>
    .nav {
        padding:0 0 var(--padding-gap);
        padding-bottom: 0;
        flex-wrap: wrap;
        &-item {
            cursor: pointer;
            font-size: var(--font-size-16);
            margin-bottom: calc(var(--padding-gap) / 2);
            margin-right: var(--padding-gap);
            &.active {
                color: var(--primary-color);
            }
        }
    }
    .list {
        padding: var(--padding-gap) 0;
        flex-wrap: wrap;
        &-item {
            width: calc(50% - var(--padding-gap)/2);
            cursor: pointer;
            margin-bottom: var(--padding-gap);
            // .title {
            //     font-size: 14px;
            // }
            img {
                height: auto;
                width: 100%;
                border-radius: var(--border-radius);
                // margin-bottom: var(--padding-gap);
            }
        }
    }
</style>
<script>
    // import { goto } from '$app/navigation'
    import Loading from '$lib/components/Loading.svelte'
    import { previewImages } from '@brewer/beerui'
    import { createEventDispatcher } from 'svelte'
	/** @type {import('./$types').PageData} */
	export let data;
    export let injClass = '';
    export let isComponent = false;
    let current = { id: data.id }
    const dispatch = createEventDispatcher()
    const onNavClick = (/** @type {{ name: string;url: string; id: number; }} */ item) => {
        current = item
        if (isComponent) {
            dispatch('cateChange', item)
            return
        }
        location.href = '/cates/wallpaper/' + item.id
    };
    $: promise = new Promise((call) => {
        if (!data.loading) {
            setTimeout(() => {
                call(data)
            }, 500);
        }
    })
    const onPreviewImages = (/** @type {string} */ src) => {
        if (isComponent) {
            dispatch('select', src)
            return
        }
        let p = previewImages(src, data.datas.list.map((/** @type {{ img: string; }} */ el) => el.img))
        // 调用方法 监听图片的改变
    }
</script>
<div class="wallpaper list {injClass}">
    <div class="nav flex-row {isComponent?'w-[100%] h-[6rem] overflow-hidden overflow-y-auto':''}">
        {#each data.cates as cate}
            <div class="nav-item {cate.id == current.id ? 'active': ''}" role={'button'} on:click={() => onNavClick(cate)}>{cate.name}</div>
        {/each}
    </div>
    {#await promise}
    <Loading text={'加载中...'} />
    {:then result}
        <div class="list flex-row just-b">
            {#each (result.datas?.list || []) as item}
                {#if item.img}
                    <div class="list-item flex-column md:!w-[24%]">
                        <img src="{item.img}" loading="lazy" role="{item.img}" on:click={() => onPreviewImages(item.img)} alt="{item.img_title || item.name}" title="{item.img_title || item.name}">
                    </div>
                {/if}
            {/each}
        </div>
    {:catch error}
    <p>{error.message}</p>
    {/await}
</div>