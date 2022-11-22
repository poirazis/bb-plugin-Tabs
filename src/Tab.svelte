<script>
    import { getContext, onMount } from "svelte"
    export let tab // object of type { id: number, name: text }
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

    $: updateBoundingBox ( $tabStore.selectedTab )
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
        <span class="spectrum-Tabs-itemLabel"> 
            {tab.name} 
        </span>
</div>

<style>
    .spectrum-Tabs-item {
      color: var(--spectrum-global-color-gray-600);
    }
</style>