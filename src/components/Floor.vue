<template>
  <div class="floor">
    <button v-on:click="$emit('changeFloorNumber', floorNumber)">
        {{floorNumber}}
    </button>
  </div>
</template>

<script>

export default {
    props:['floorNumber'],
    data(){
        return {
        }
    },
    components: {},
    methods: {},
}
</script>

<style>
.floor{
    width: 700px;
    height: 50px;
    outline: 1px solid #000;
}
</style>
