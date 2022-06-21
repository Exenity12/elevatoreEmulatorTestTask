<template>
  <div class="mineElevator">
    <div 
        class="elevator"
        v-bind:style="{bottom: (numberFloor - 1) * 50 + 'px'}"
        v-bind:class="{wait: elevatorIsWait}">
        {{purposeOfTheMovement}}
        {{moveDirectionElevator}}
    </div>
  </div>
</template>

<script>

export default {
    props:['numberFloor', 'purposeOfTheMovement', 'moveDirectionElevator', 'elevatorIsWait'],
    data(){
        return {

        }
    },
    components: {},
    methods: {},
}
</script>

<style>
.mineElevator{
  position: relative;
  width: 50px;
  outline: 1px solid #000;
}

.elevator{
  position: absolute;
  margin: 1px;
  width: 48px;
  height: 48px;
  background-color: red;
  outline: 1px solid #000;
  transition-duration: 1s;
}

.wait {
  animation:1s blinker linear infinite;
}

@keyframes blinker {
0% { opacity: 1.0; }
50% { opacity: 0.0; }
100% { opacity: 1.0; }
}


</style>
