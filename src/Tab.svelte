<script>
    import { getContext, onMount } from "svelte"
    export let tab // object of type { id: number, name: text, icon: text }
    export let iconSize
    export let iconsOnly
    export let emphasized
    export let emphasizedColor = "var(--spectrum-global-color-indigo-600)"

    let isSelected
    let tabColor 
    let tabBox

    const tabStore = getContext("tabStore")

    function updateBoundingBox ()
    {
        if ( $tabStore.selectedTab === tab.id )
        {   
            $tabStore.selectedTabBox = tabBox?.getBoundingClientRect()
            isSelected = true 
        } else {
            isSelected = false
        }
    }

    const onClick = () => {
        $tabStore.selectedTab = tab.id;
    }

    $: updateBoundingBox ( $tabStore.selectedTab, $tabStore.direction, $tabStore.size )
    $: if (isSelected) {
        tabColor = "var(--spectrum-global-color-gray-900)"
        if (emphasized) {
            tabColor = emphasizedColor;
        } 
    } else {
        tabColor = "var(--spectrum-global-color-gray-600)"
    }
    
    onMount( () => updateBoundingBox() )
   
</script>
<!-- svelte-ignore a11y-click-events-have-key-events -->
<!-- svelte-ignore a11y-no-noninteractive-tabindex -->
<div
    bind:this={tabBox}
    on:click={onClick}
    class="spectrum-Tabs-item" tabindex="0"
    style="color: {tabColor};"
    >
        {#if tab.icon }
            <i class="{tab.icon} {iconSize}"/>
        {/if}
        {#if !iconsOnly}
            <span class="spectrum-Tabs-itemLabel"> {tab.name} </span>
        {/if}
</div>

<style>
    .spectrum-Tabs-item {
      display: flex;
      flex-direction: row;
      padding: 0px 4px;
      color: var(--spectrum-global-color-gray-600);
      align-items: center;
      gap: 8px;      
    }
</style>