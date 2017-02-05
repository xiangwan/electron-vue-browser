<template> 
 <chrome/>  
</template>
<script>
  import store from 'renderer/vuex/store'
  import Chrome from 'renderer/components/Chrome'
  export default {
    components: {
      Chrome
    },
    store
  }
</script>
<style>
   :root {
    --border-radius: 4px;
  }
  
  body {
    margin: 0;
    padding: 0;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Helvetica, Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol"; 
  }
  
  html,
  body {
    height: 100%;
     overflow: hidden;
  }
  
  ul {
    margin: 0;
    padding: 0;
  }
  
  li {
    list-style: none;
  }
  
  :global(#root) {
    height: 100%;
    padding: 24px;
  }
  
  *,
  *::before,
  *::after {
    box-sizing: border-box;
  } 
</style>