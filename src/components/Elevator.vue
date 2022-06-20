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
    position: absolute;
    width: 50px;
    height: 100%;
    background-color: black;
}

.elevator{
    position: absolute;
    width: 50px;
    height: 50px;
    background-color: red;
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
