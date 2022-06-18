<template>
  <div>
    <div class="mainBoard">
      <Elevator 
        v-bind:numberFloor="elevatorPosition"
        v-bind:purposeOfTheMovement="purposeOfTheMovement"
        v-bind:moveDirectionElevator="moveDirectionElevator"
      />
      <Floor 
        v-for="item in floors"
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
    let floorCount = 5;
    let buttonId = 0;
    let floors = [];
    while(floorCount > 0){
      floors.push({
        number: floorCount,
        id: buttonId,
        active: false,
      });
      floorCount--;
      buttonId++;
    }
    return {
      alredyInProgres: true,
      elevatorTimeOut: 1000,
      elevatorPosition: 1,
      floors,
      arrayWayElevator: [1],
      arrayWayElevatorInAllFloor: [],
      localArrayWayElevator: [],
      matchingFloors: false,
      moveDirectionElevator: "",
      purposeOfTheMovement: "",
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

    moveElevator(){
      if(this.arrayWayElevatorInAllFloor.length == 1){
        return;
      }
      this.arrayWayElevatorInAllFloor.splice(0, 1);
      this.elevatorPosition = this.arrayWayElevatorInAllFloor[0];
      console.log(this.arrayWayElevatorInAllFloor)
    },

    addFloorNumber(number, buttonId){
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
      this.floors[buttonId].active = true;
      this.passedFloors(this.arrayWayElevator[0], this.arrayWayElevator[1]).forEach((item) => {
        this.arrayWayElevatorInAllFloor.push(item);
      });
      this.purposeOfTheMovement = this.arrayWayElevatorInAllFloor[this.arrayWayElevatorInAllFloor.length - 1];
      if(this.alredyInProgres){
        setInterval(this.moveElevator, this.elevatorTimeOut);
        this.alredyInProgres = false;
      };
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
  float: left;
  top: 200px;
  left: 200px;
}

</style>
