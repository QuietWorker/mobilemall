<template>
  <div id="tab-bar">
        <slot></slot>
    </div>
</template>

<script>


export default {
    name:'TabBar'
}
</script>

<style>

  #tab-bar{
    display: flex;
    background-color: #f5f5f5;
    position: fixed;
    bottom: 0px;
    left: 0;
    right: 0;
    box-shadow: 0 -3px 1px rgba(100,100,100,.2);

  }
  
</style>