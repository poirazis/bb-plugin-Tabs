<script>
  import "@spectrum-css/tabs/dist/index-vars.css"
  import { getContext, onMount, setContext } from "svelte"
  import { writable } from "svelte/store"
  import Tab from "./Tab.svelte"

  export let size = "M"
  export let centered = false
  export let vertical = false
  export let quiet = false
  export let compact = false
  export let emphasized = false
  export let emphasizedColor = "var(--spectrum-global-color-indigo-600)"

  // Internal 
  let container
  $: styles = { 
    "pos_justify": centered ? "center" : "flex-start"
  };

  $: cssVarStyles = Object.entries(styles)
		.map(([key, value]) => `--${key}:${value}`)
		.join(';');

  $: emphasizedColor = emphasized ? emphasizedColor : "var(--spectrum-global-color-gray-600";

  const { styleable } = getContext("sdk")
  const component = getContext("component")
  
  const tabStore = writable({
    tabs: [],
    selectedTab: 0,
    selectedTabBox: {},
    registerTab: function ( id, name ) { 
      this.tabs.push ({id:id, name:name});
      this.selectedTab = id;
    },
    unregisterTab: function ( id ) { 
        var indx = this.tabs.findIndex(v => v.id === id);
        this.tabs.splice(indx, indx >= 0 ? 1 : 0);
     }, 
    updateTab: function ( id, name ) { 
        var indx = this.tabs.findIndex(v => v.id === id);
        if (indx > -1) { 
          this.tabs[indx].name = name;        }
    }
  });
  setContext("tabStore", tabStore);


  let top, left, width, height
  $: calculateIndicator($tabStore.selectedTab, centered)


  function setActiveTab (){
    if ($tabStore.tabs.length > 0)
      $tabStore.selectedTab = $tabStore.tabs[0].id;
  }

  function calculateIndicator() {
      let tabBox = $tabStore?.selectedTabBox;
      if ( tabBox ) {
      if (!vertical) {
        width = tabBox?.width + "px"
        height = "2px" 
        left = tabBox?.left - container?.getBoundingClientRect().left + "px"
      } else {
        height = tabBox?.height + 4 + "px"
        width = "2px"
        top = tabBox?.top - container?.getBoundingClientRect().top + "px"
      }
    }
  }

  onMount(() => setActiveTab())
</script>

<div use:styleable={$component.styles} class="container" >
  {#if ($tabStore.tabs.length > 0)}
  <div bind:this={container}
      class:spectrum-Tabs--quiet={quiet}
      class:spectrum-Tabs--vertical={vertical}
      class:spectrum-Tabs--horizontal={!vertical}
      class:spectrum-Tabs--compact={compact}
      class="spectrum-Tabs spectrum-Tabs--size{size}"
      style="{cssVarStyles}"
      >

      {#each $tabStore.tabs as tab }
        <Tab {tab} {emphasized} {emphasizedColor} />
      {/each}

      <div
        class="spectrum-Tabs-selectionIndicator"
        style="background-color: {emphasizedColor}; width: {width}; height: {height}; left: {left}; top: {top};"
      />
  </div>
  {:else}
    <div> Please add some Tab Containers to get started </div>
  {/if}
  <div class="spectrum-Tabs-content">
    <slot />
  </div>
</div>


<style>

.spectrum-Tabs--quiet {
  display: flex;
  border-bottom: transparent !important;
  justify-content: var(--pos_justify) !important;
}
.spectrum-Tabs {
  position: relative;
  border-bottom-color: var(--spectrum-global-color-gray-200);
  justify-content: var(--pos_justify);
}
.spectrum-Tabs-content {
  margin-top: var(--spectrum-global-dimension-static-size-150);
}
.spectrum-Tabs-selectionIndicator {
  transition: all 200ms;
  background-color: var(--spectrum-global-color-gray-900);
}
</style>
