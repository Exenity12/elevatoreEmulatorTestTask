<template>
  <div>
    <div class="mainBoard">
      <Elevator
        v-for="item in arrayElevator"
        v-bind:numberFloor="item.elevatorPosition"
        v-bind:purposeOfTheMovement="item.purposeOfTheMovement"
        v-bind:moveDirectionElevator="item.moveDirectionElevator"
        v-bind:elevatorIsWait="item.elevatorIsWait"
      />
      <div>
        <Floor 
          v-for="item in floors"
          v-bind:floorNumber="item.number"
          v-bind:buttonActive="item.active"
          v-bind:buttonActiveId="item.id"
          v-on:changeFloorNumber="addFloorNumber"
        />
      </div>
    </div>
  </div>
</template>

<script>
import Floor from "@/components/Floor"
import Elevator from "@/components/Elevator"



export default {
  name: 'App',
  data(){
    let floorCount = 5; // Количество этажей
    let elevatorCount = 3; // Количество лифтов
    let arrayElevator = [];
    let buttonId = 0;
    let elevatorId = 0;
    let floors = [];
    while(floorCount > 0){
      floors.push({
        number: floorCount,
        id: buttonId,
        active: false,
      });
      floorCount--;
      buttonId++;
    };
    while(elevatorCount > 0){
      arrayElevator.push({
        elevatorId: elevatorId,
        elevatorPosition: 1,
        moveDirectionElevator: "",
        purposeOfTheMovement: "",
        elevatorIsWait: false,
        arrayWayElevatorInAllFloor: [],
        elevatorMovementTimer: ""
      });
      elevatorId++;
      elevatorCount--;
    };
    return {
      arrayElevator,
      alredyInProgres: true,
      elevatorTimeOut: 1000,
      floors,
      arrayWayElevator: [1],
      arrayWayElevatorInAllFloor: [],
      localArrayWayElevator: [],
      matchingFloors: false,
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
      console.log(this.arrayElevator)
      if(this.inactiveButton){
        let arr = this.floors.find(item => item.number == this.arrayElevator[0].arrayWayElevatorInAllFloor[0]);
        if(this.arrayElevator[0].arrayWayElevatorInAllFloor[0] == this.arrayElevator[0].arrayWayElevatorInAllFloor[1]){
          arr.active = false;
          this.inactiveButton = false;
          this.arrayElevator[0].elevatorIsWait = true;
        };
      }
      if(this.arrayElevator[0].arrayWayElevatorInAllFloor.length == 1){
        this.arrayElevator[0].elevatorIsWait = false;
        this.arrayElevator[0].purposeOfTheMovement = "";
        this.arrayElevator[0].moveDirectionElevator = "";
        return;
      }
      this.arrayElevator[0].arrayWayElevatorInAllFloor.splice(0, 1);
      let loopIteration = 0;
      while(loopIteration < this.arrayElevator[0].arrayWayElevatorInAllFloor.length){
        if(this.arrayElevator[0].arrayWayElevatorInAllFloor[loopIteration] == this.arrayElevator[0].arrayWayElevatorInAllFloor[loopIteration + 1]){
          this.arrayElevator[0].purposeOfTheMovement = this.arrayElevator[0].arrayWayElevatorInAllFloor[loopIteration];
          if(this.arrayElevator[0].elevatorPosition < this.arrayElevator[0].arrayWayElevatorInAllFloor[loopIteration]) {
            this.arrayElevator[0].moveDirectionElevator = "↑";
            this.arrayElevator[0].elevatorIsWait = false;
          } else if(this.arrayElevator[0].elevatorPosition > this.arrayElevator[0].arrayWayElevatorInAllFloor[loopIteration]){
            this.arrayElevator[0].moveDirectionElevator = "↓";
            this.arrayElevator[0].elevatorIsWait = false;
          };
          break;
        };
        loopIteration++;
      }
      if(this.arrayElevator[0].arrayWayElevatorInAllFloor[0] == this.arrayElevator[0].arrayWayElevatorInAllFloor[1]){
        this.inactiveButton = true;
      };
      this.arrayElevator[0].elevatorPosition = this.arrayElevator[0].arrayWayElevatorInAllFloor[0];
      let iteratingTheArray = 0;
    },
     
    addFloorNumber(number, buttonId){
      if(this.floors[buttonId].active == true) return
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
        this.arrayElevator[0].arrayWayElevatorInAllFloor.push(item);
      });
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

.mainBoard {
  display: flex;
  flex-direction: row;
  text-align: center;
  position: absolute;
  float: left;
  top: 200px;
  left: 500px;
}

</style>