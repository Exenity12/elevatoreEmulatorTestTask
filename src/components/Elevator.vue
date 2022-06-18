<template>
  <div class="mineElevator">
    <div 
        class="elevator"
        v-bind:class='{firstFloor: numberFloor == 1,  secondFloor: numberFloor == 2, thirdFloor: numberFloor == 3, fourthFloor: numberFloor == 4, fifthFloor: numberFloor == 5}'>
        {{numberFloor}}
        {{moveDirectionElevator}}
    </div>
  </div>
</template>

<script>

export default {
    props:['numberFloor', "moveDirectionElevator"],
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
}

.firstFloor{
    position: absolute;
    top: 200px;
    transition-duration: 1s;
}
.secondFloor{
    position: absolute;
    top: 150px;
    transition-duration: 1s;
}
.thirdFloor{
    position: absolute;
    top: 100px;
    transition-duration: 1s;
}
.fourthFloor{
    position: absolute;
    top: 50px;
    transition-duration: 1s;
}
.fifthFloor{
    position: absolute;
    top: 0px;
    transition-duration: 1s;
}



</style>
