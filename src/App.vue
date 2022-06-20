<template>
  <div>
    <div class="mainBoard">
      <Elevator 
        v-bind:numberFloor="elevatorPosition"
        v-bind:purposeOfTheMovement="purposeOfTheMovement"
        v-bind:moveDirectionElevator="moveDirectionElevator"
        v-bind:elevatorIsWait="elevatorIsWait"
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
      elevatorIsWait: false,
      floors,
      arrayWayElevator: [1],
      arrayWayElevatorInAllFloor: [],
      localArrayWayElevator: [],
      matchingFloors: false,
      moveDirectionElevator: "",
      purposeOfTheMovement: "",
      inactiveButton: false,

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
        this.localArrayWayElevator.push(oneNumber - 1);
      } else if(oneNumber > twoNumber){
        while(oneNumber >= twoNumber){
          this.localArrayWayElevator.push(oneNumber);
          oneNumber--;
        };
        this.localArrayWayElevator.push(oneNumber + 1);
      };
      return this.localArrayWayElevator;
    },

    moveElevator(){
      if(this.inactiveButton){
        let arr = this.floors.find(item => item.number == this.arrayWayElevatorInAllFloor[0]);
        if(this.arrayWayElevatorInAllFloor[0] == this.arrayWayElevatorInAllFloor[1]){
          arr.active = false;
          this.inactiveButton = false;
          this.elevatorIsWait = true;
        };
      }
      if(this.arrayWayElevatorInAllFloor.length == 1){
        this.elevatorIsWait = false;
        this.purposeOfTheMovement = "";
        this.moveDirectionElevator = "";
        return;
      }
      this.arrayWayElevatorInAllFloor.splice(0, 1);
      console.log(this.arrayWayElevatorInAllFloor)
      let loopIteration = 0;
      while(loopIteration < this.arrayWayElevatorInAllFloor.length){
        if(this.arrayWayElevatorInAllFloor[loopIteration] == this.arrayWayElevatorInAllFloor[loopIteration + 1]){
          this.purposeOfTheMovement = this.arrayWayElevatorInAllFloor[loopIteration];
          if(this.elevatorPosition < this.arrayWayElevatorInAllFloor[loopIteration]) {
            this.moveDirectionElevator = "↑";
            this.elevatorIsWait = false;
          } else if(this.elevatorPosition > this.arrayWayElevatorInAllFloor[loopIteration]){
            this.moveDirectionElevator = "↓";
            this.elevatorIsWait = false;
          };
          break;
        };
        loopIteration++;
      }
      if(this.arrayWayElevatorInAllFloor[0] == this.arrayWayElevatorInAllFloor[1]){
        this.inactiveButton = true;
      };
      this.elevatorPosition = this.arrayWayElevatorInAllFloor[0];
      let iteratingTheArray = 0;
    },
     
    addFloorNumber(number, buttonId){
      console.log(number)
      this.localArrayWayElevator = [];
      this.matchingFloors = false;
      if(this.arrayWayElevator){
        this.arrayWayElevator.forEach((item) => {
          if(item == number) this.matchingFloors = true;
        });
      };
      if(this.matchingFloors) return;
      this.arrayWayElevator.push(number);
      this.floors[buttonId].active = true;
      this.passedFloors(this.arrayWayElevator[0], this.arrayWayElevator[1]).forEach((item) => {
        this.arrayWayElevatorInAllFloor.push(item);
      });
      if(this.alredyInProgres){
        setInterval(this.moveElevator, this.elevatorTimeOut);
        this.alredyInProgres = false;
      };
      this.arrayWayElevator.splice(0, 1);
    },
  },
}



// сохранение состояния
// разное кол-во elevators
// 



</script>

<style>

.mainBoard {
  text-align: center;
  position: absolute;
  float: left;
  top: 200px;
  left: 200px;
}

</style>