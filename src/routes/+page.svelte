<script lang='ts'>
    import type {PageData} from "./$types";
    import type {IndexMonster} from "./+page";
    import { page } from "$app/stores";
    import { goto } from '$app/navigation';
    import {generations} from "./generations";
    import Monster from "./Monster.svelte";

    export let data: PageData;

    let form = {
        searchString: ''
    }

    let  searchString = ''
    $: selectedMonsters = data.monsters.filter((monster) => {
        return monster.name.toLowerCase().includes(searchString.toLowerCase());
    })

    $: monsterId = $page.url.searchParams.get('monsterId') || '';
    $: monster = data.monsters.find(monster => monster.id === monsterId);
    $: monsterId2 = $page.url.searchParams.get('monsterId2') || '';
    $: monster2 = data.monsters.find(monster => monster.id === monsterId2);
    $: selectedGenerationId = $page.url.searchParams.get('generation_id') || '';

    const updateSearchParams = (key: string, value: string) => {
        const searchParams = new URLSearchParams($page.url.searchParams);
        searchParams.set(key, value);
        goto(`?${searchParams.toString()}`);
    }

    const submitSearch = (event: Event) => {
        searchString = form.searchString;
    }

</script>

{#if monster}
<Monster 
    monster={monster}
    updateSearchParams={updateSearchParams}
/>
{/if}
{#if monster2}
<Monster 
    monster={monster2}
    updateSearchParams={updateSearchParams}
/>
{/if}



<div class="generations">
    <button 
        class="generation"
        class:active={selectedGenerationId == 'all'}
        on:click={() => updateSearchParams('generation_id', 'all')}
    >All</button>
    {#each generations as generation (generation.id)}
        <button 
            class="generation"
            class:active={selectedGenerationId === generation.id.toString()}
            on:click={() => updateSearchParams('generation_id', generation.id.toString())}
        >
            {generation.main_region}
        </button>
    {/each}
</div>

<form class="search-form" on:submit|preventDefault={submitSearch}>
    <input class="search-field" type="text" bind:value={form.searchString} placeholder="Pokemon Name"/>
    <input type="submit" value="Search">
</form>

<div class="monsters">
    {#each selectedMonsters as monster (monster.id)}
        <Monster 
        monster={monster} 
        updateSearchParams={updateSearchParams}
        isInteractive={true}
        />
    {/each}
</div>


<style>

    .generations {
        display: flex;
        flex-direction: row;
        flex-wrap: wrap;
        justify-content: center;
    }

    .generation {
        margin: 5px;
        padding: 5px 10px;
        border: 2px solid #aaa;
        border-radius: 6px;
        background-color: #f9f9f9;
        color: #333;
        cursor: pointer;
    }

    .generation.active {
        background-color: #707070;
        color: white;
    }

    .generation:hover {
        background-color: #f0f0f0;
    }

    .monsters {
        display: flex;
        flex-direction: row;
        flex-wrap: wrap;
        justify-content: center;
    }

    .search-form input[type="text"] {
        padding: 5px 10px;
        border-radius: 5px;
        width: 200px;
    }

    .search-form input[type="submit"] {
        padding: 5px 10px;
        border-radius: 5px;
        margin-left: 10px;
        background-color: #f9f9f9;
        border: 1px solid #aaa;
        cursor: pointer;
    }

    .search-form {
        display: flex;
        justify-content: center;
        margin: 20px 0;
    }

</style>