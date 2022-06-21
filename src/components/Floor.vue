<template>
  <div class="floor">
    <button
      v-on:click="$emit('changeFloorNumber', floorNumber, buttonActiveId)"
      v-bind:class='{active: buttonActive}'>
        {{floorNumber}}
    </button>
  </div>
</template>

<script>

export default {
    props:['floorNumber', "buttonActive", "buttonActiveId"],
    components: {},
    methods: {},
}
</script>

<style>
.floor{
  position: relative;
  width: 300px;
  height: 50px;
  outline: 1px solid #000;
}

.active{
  background-color: blue;
}
</style>
