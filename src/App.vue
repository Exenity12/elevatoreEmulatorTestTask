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
    let floorCount = 8; // Количество этажей
    let elevatorCount = 4; // Количество лифтов
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
      arrayWayElevator: [1],
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


      if(this.arrayElevator[index].arrayWayElevatorInAllFloor[0] == this.arrayElevator[index].arrayWayElevatorInAllFloor[1]){
        let arr = this.floors.find(item => item.number == this.arrayElevator[index].arrayWayElevatorInAllFloor[0]);
        if(this.arrayElevator[index].arrayWayElevatorInAllFloor[0] == this.arrayElevator[index].arrayWayElevatorInAllFloor[1]){
          arr.active = false;
          this.inactiveButton = false;
          this.arrayElevator[index].elevatorIsWait = true;
        };
      };
      if(this.arrayElevator[index].arrayWayElevatorInAllFloor.length == 1){
        this.arrayElevator[index].elevatorIsWait = false;
        this.arrayElevator[index].purposeOfTheMovement = "";
        this.arrayElevator[index].moveDirectionElevator = "";
        return;
      };
      this.arrayElevator[index].arrayWayElevatorInAllFloor.splice(0, 1);
      let loopIteration = 0;
      while(loopIteration < this.arrayElevator[index].arrayWayElevatorInAllFloor.length){
        if(this.arrayElevator[index].arrayWayElevatorInAllFloor[loopIteration] == this.arrayElevator[index].arrayWayElevatorInAllFloor[loopIteration + 1]){
          this.arrayElevator[index].purposeOfTheMovement = this.arrayElevator[index].arrayWayElevatorInAllFloor[loopIteration];
          if(this.arrayElevator[index].elevatorPosition < this.arrayElevator[index].arrayWayElevatorInAllFloor[loopIteration]) {
            this.arrayElevator[index].moveDirectionElevator = "↑";
            this.arrayElevator[index].elevatorIsWait = false;
          } else if(this.arrayElevator[index].elevatorPosition > this.arrayElevator[index].arrayWayElevatorInAllFloor[loopIteration]){
            this.arrayElevator[index].moveDirectionElevator = "↓";
            this.arrayElevator[index].elevatorIsWait = false;
          };
          break;
        };
        loopIteration++;
      };



      this.arrayElevator[index].elevatorPosition = this.arrayElevator[index].arrayWayElevatorInAllFloor[0];
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
      console.log(arrayClonFloorsWhenIsElevator)
      while(indexInArrayElevators < this.arrayElevator.length){
        this.indexNearestElevator = arrayFloorWhenIsElevator.indexOf(nearestNumber);
        if(this.arrayElevator[this.indexNearestElevator].arrayWayElevatorInAllFloor.length > 1){
          indexOfTheNearestNumbers++;
          nearestNumber = arrayClonFloorsWhenIsElevator.sort((x, y) => Math.abs(number - x) - Math.abs(number - y))[indexOfTheNearestNumbers];
        }
        indexInArrayElevators++;
      };
      










      this.arrayElevator[this.indexNearestElevator].arrayWayElevator.push(number);
      this.passedFloors(this.arrayElevator[this.indexNearestElevator].arrayWayElevator[0], number).forEach((item) => {
        console.log(item)
        this.arrayElevator[this.indexNearestElevator].arrayWayElevatorInAllFloor.push(item);
      });
      console.log(this.arrayElevator[this.indexNearestElevator].arrayWayElevatorInAllFloor);
      this.arrayElevator[this.indexNearestElevator].arrayWayElevator.splice(0, 1);
      if(this.arrayElevator[this.indexNearestElevator].alredyInProgres){
        this.arrayElevator[this.indexNearestElevator].elevatorMovementTimer = setInterval(this.moveElevator, this.elevatorTimeOut, this.indexNearestElevator);
        this.arrayElevator[this.indexNearestElevator].alredyInProgres = false;
      };
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