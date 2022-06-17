<template>
  <div class="mainBoard">
    <div>
      <Elevator />
      <Floor 
        v-for="item in floor" :key="floor.number"
        v-bind:floorNumber="item.number"
        v-on:changeFloorNumber="addFloorNumber"
      />
    </div>
  </div>
</template>

<script>
import Floor from "@/components/Floor"
import Elevator from "@/components/Elevator"
export default {
  name: 'App',
  data(){
    return {
      elevatorPosition: 1,
      floor: [
        {number: 5, completed: false},
        {number: 4, completed: false},
        {number: 3, completed: false},
        {number: 2, completed: false},
        {number: 1, completed: false},
      ],
      arrayWayElevator: [],
      matchingFloors: false,
    }
  },
  components: { Floor, Elevator },
  methods: {
    addFloorNumber(number){
      this.matchingFloors = false;
      if(number == this.elevatorPosition) return;
      if(this.arrayWayElevator){
        this.arrayWayElevator.forEach((item) => {
          if(item == number) this.matchingFloors = true;
        })
      };
      if(this.matchingFloors) return;
      this.arrayWayElevator.push(number);
      console.log(this.arrayWayElevator);
    }
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

nav {
  padding: 30px;
}

nav a {
  font-weight: bold;
  color: #2c3e50;
}

nav a.router-link-exact-active {
  color: #42b983;
}

.mainBoard {
  position: absolute;
  top: 200px;
  left: 200px;
}
</style>
