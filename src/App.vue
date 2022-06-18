<template>
  <div>
    <div class="mainBoard">
      <Elevator 
        v-bind:numberFloor="elevatorPosition"
        v-bind:moveDirectionElevator="moveDirectionElevator"
      />
      <Floor 
        v-for="item in floor"
        v-bind:floorNumber="item.number"
        v-bind:buttonActive="item.active"
        v-bind:buttonActiveId="item.id"
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
        {number: 5, id: 0, active: false},
        {number: 4, id: 1, active: false},
        {number: 3, id: 2, active: false},
        {number: 2, id: 3, active: false},
        {number: 1, id: 4, active: false},
      ],
      arrayWayElevator: [],
      localArrayWayElevator: [],
      matchingFloors: false,
      moveDirectionElevator: "",
    }
  },
  components: { Floor, Elevator },
  methods: {

    passedFloors(oneNumber, twoNumber){
      if(oneNumber < twoNumber){
        while(oneNumber <= twoNumber){
          this.localArrayWayElevator.push(oneNumber);
          oneNumber++;
        };
      } else if(oneNumber > twoNumber){
        while(oneNumber >= twoNumber){
          this.localArrayWayElevator.push(oneNumber);
          oneNumber--;
        };
      };
      return this.localArrayWayElevator;
    },


    addFloorNumber(number, index){
      this.localArrayWayElevator = [];
      this.matchingFloors = false;
      if(number == this.elevatorPosition) return;
      if(this.arrayWayElevator){
        this.arrayWayElevator.forEach((item) => {
          if(item == number) this.matchingFloors = true;
        });
      };
      if(this.matchingFloors) return;
      if(this.elevatorPosition < number) this.moveDirectionElevator = "↑";
      if(this.elevatorPosition > number) this.moveDirectionElevator = "↓";
      this.arrayWayElevator.push(number);
      this.floor[index].active = true;
      console.log(this.passedFloors(this.elevatorPosition, number));
      // this.elevatorPosition = number;
      this.passedFloors(this.elevatorPosition, this.arrayWayElevator[0]).forEach((localNumberFloor) => {
        setTimeout(() => {
          this.elevatorPosition = localNumberFloor;
          console.log(this.elevatorPosition);
        }, 1000);
      });
      this.arrayWayElevator.splice(0, 1);
    },
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
