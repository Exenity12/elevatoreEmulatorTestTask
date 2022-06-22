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
    let elevatorCount = 1; // Количество лифтов
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
        elevatorMovementTimer: "",
        elevatorIsWait: false,
        alredyInProgres: true,
        arrayWayElevator: [1],
        arrayWayElevatorInAllFloor: [],
      });
      elevatorId++;
      elevatorCount--;
    };
    return {
      floors,
      arrayElevator,
      indexNearestElevator: 0,
      activeElevator: 0,
      elevatorTimeOut: 1000,
      localArrayWayElevator: [],
      matchingFloors: false,
      inactiveButton: false,
      alredyInProgres: true,
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

    moveElevator(index){
      let localArrayElevatorposition = [];
      this.arrayElevator.forEach((eleavtor) => {
        localArrayElevatorposition.push(eleavtor.elevatorPosition);
      });
      localStorage.setItem('arrayElevator', localArrayElevatorposition);
      const nearest = this.arrayElevator[index];
      if(nearest.arrayWayElevatorInAllFloor[0] == nearest.arrayWayElevatorInAllFloor[1]){
        let arr = this.floors.find(item => item.number == nearest.arrayWayElevatorInAllFloor[0]);
        if(nearest.arrayWayElevatorInAllFloor[0] == nearest.arrayWayElevatorInAllFloor[1]){
          arr.active = false;
          this.inactiveButton = false;
          nearest.elevatorIsWait = true;
        };
      };
      if(nearest.arrayWayElevatorInAllFloor.length == 1){
        nearest.elevatorIsWait = false;
        nearest.purposeOfTheMovement = "";
        nearest.moveDirectionElevator = "";
        return;
      };
      nearest.arrayWayElevatorInAllFloor.splice(0, 1);
      let loopIteration = 0;
      while(loopIteration < nearest.arrayWayElevatorInAllFloor.length){
        if(nearest.arrayWayElevatorInAllFloor[loopIteration] == nearest.arrayWayElevatorInAllFloor[loopIteration + 1]){
          nearest.purposeOfTheMovement = nearest.arrayWayElevatorInAllFloor[loopIteration];
          if(nearest.elevatorPosition < nearest.arrayWayElevatorInAllFloor[loopIteration]) {
            nearest.moveDirectionElevator = "↑";
            nearest.elevatorIsWait = false;
          } else if(nearest.elevatorPosition > nearest.arrayWayElevatorInAllFloor[loopIteration]){
            nearest.moveDirectionElevator = "↓";
            nearest.elevatorIsWait = false;
          };
          break;
        };
        loopIteration++;
      };
      nearest.elevatorPosition = nearest.arrayWayElevatorInAllFloor[0];
    },

    addFloorNumber(number, buttonId){
      this.localArrayWayElevator = [];
      let arrayFloorWhenIsElevator = [];
      let arrayClonFloorsWhenIsElevator = ""
      let indexOfTheNearestNumbers = 0;
      this.indexNearestElevator = 0;
      let indexInArrayElevators = 0;
      let indexInArrayWayInAllFloors = 0;
      this.matchingFloors = false;
      this.arrayElevator.forEach((elevator) => {
        elevator.arrayWayElevator.forEach((way) => {
          if(way == number) {
            this.matchingFloors = true;
            return;
          };
        });
        arrayFloorWhenIsElevator.push(elevator.elevatorPosition);
      });
      if(this.matchingFloors) return;
      this.floors[buttonId].active = true;
      arrayClonFloorsWhenIsElevator = [...arrayFloorWhenIsElevator];
      indexOfTheNearestNumbers = 0;
      let nearestNumber = arrayClonFloorsWhenIsElevator.sort((x, y) => Math.abs(number - x) - Math.abs(number - y))[indexOfTheNearestNumbers];
      while(indexInArrayElevators < this.arrayElevator.length){
        this.indexNearestElevator = arrayFloorWhenIsElevator.indexOf(nearestNumber);
        if(this.arrayElevator.length == 1) break;
        if(this.arrayElevator[this.indexNearestElevator].arrayWayElevatorInAllFloor.length > 1){
          indexOfTheNearestNumbers ++;
          this.indexNearestElevator++;
          nearestNumber = arrayClonFloorsWhenIsElevator.sort((x, y) => Math.abs(number - x) - Math.abs(number - y))[indexOfTheNearestNumbers];
        }
        if(this.indexNearestElevator == this.arrayElevator.length) this.indexNearestElevator = 0;
        indexInArrayElevators++;
      };
      const elevator = this.arrayElevator[this.indexNearestElevator];
      elevator.arrayWayElevator.push(number);
      this.passedFloors(elevator.arrayWayElevator[0], number).forEach((item) => {
        elevator.arrayWayElevatorInAllFloor.push(item);
      });
      elevator.arrayWayElevator.splice(0, 1);
      if(elevator.alredyInProgres){
        elevator.elevatorMovementTimer = setInterval(this.moveElevator, this.elevatorTimeOut, this.indexNearestElevator);
        elevator.alredyInProgres = false;
      };
    }, 
  },
  beforeMount(){
    let iteration = 0;
    let iterationLocalStor = 0;
    if(localStorage.arrayElevator){
      while(iteration < this.arrayElevator.length){
        if(isNaN(+localStorage.arrayElevator.split(',')[iteration])){
          this.arrayElevator[iteration].elevatorPosition = [1];
          this.arrayElevator[iteration].arrayWayElevator = [1];
        };
        if(!isNaN(+localStorage.arrayElevator.split(',')[iteration])){
          this.arrayElevator[iteration].elevatorPosition = [Number(localStorage.arrayElevator[iterationLocalStor])];
          this.arrayElevator[iteration].arrayWayElevator = [Number(localStorage.arrayElevator[iterationLocalStor])];
        };
        if(this.arrayElevator[iteration].elevatorPosition > this.floors.length) {
          this.arrayElevator[iteration].elevatorPosition = [1];
          this.arrayElevator[iteration].arrayWayElevator = [1];
        };
        iteration++;
        iterationLocalStor += 2;
      };
    };
    let localArrayElevatorposition = [];
    this.arrayElevator.forEach((eleavtor) => {
      localArrayElevatorposition.push(eleavtor.elevatorPosition);
    });
    localStorage.setItem('arrayElevator', localArrayElevatorposition);
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