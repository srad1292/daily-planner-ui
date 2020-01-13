<template>
  <div class="login">
    <h1>This is a login page</h1>
    <form>
      <b-row>
        <b-col cols="4"></b-col>
        <b-col cols="1">
          <b-form-select v-model="startHours" :options="hoursOptions"></b-form-select>
        </b-col>
        <b-col cols="1">
          <b-form-select v-model="startMinutes" :options="minutesOptions"></b-form-select>
        </b-col>
        <b-col cols="1">
          <b-form-select v-model="startPeriod" :options="periodOptions"></b-form-select>
        </b-col>
      </b-row>
      <b-row>
        <b-col cols="4"></b-col>
        <b-col cols="1">
          <b-form-select v-model="endHours" :options="hoursOptions"></b-form-select>
        </b-col>
        <b-col cols="1">
          <b-form-select v-model="endMinutes" :options="minutesOptions"></b-form-select>
        </b-col>
        <b-col cols="1">
          <b-form-select v-model="endPeriod" :options="periodOptions"></b-form-select>
        </b-col>
      </b-row>
      <b-row>
        <b-col cols="5"></b-col>
        <b-col cols="1">
          <button
            class="arrow-button"
            type="submit"
            v-on:click.stop.prevent="addItem()"
            :disabled="tryingToAdd"
          >
            Add Item
          </button>
        </b-col>
      </b-row>
    </form>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import moment from "moment";

@Component
export default class Home extends Vue {
  startHours: string;
  startMinutes: string;
  startPeriod: string;
  endHours: string;
  endMinutes: string;
  endPeriod: string;
  tryingToAdd: boolean;
  items: any[];

  hoursOptions: any[];
  minutesOptions: any[];
  periodOptions: any[];

  displayError: boolean;
  errorMessage: string;


  constructor() {
    super();
    this.startHours = "1";
    this.startMinutes = "00";
    this.startPeriod = "am";
    this.endHours = "2";
    this.endMinutes = "00";
    this.endPeriod = "am";
    this.tryingToAdd = false;
    this.items = [[],[],[]];
    this.displayError = false;
    this.errorMessage = "";

    this.hoursOptions = [
      { value: "1", text: '1' },
      { value: "2", text: '2' },
      { value: "3", text: '3' },
      { value: "4", text: '4' },
      { value: "5", text: '5' },
      { value: "6", text: '6' },
      { value: "7", text: '7' },
      { value: "8", text: '8' },
      { value: "9", text: '9' },
      { value: "10", text: '10' },
      { value: "11", text: '11' },
      { value: "12", text: '12' }
    ];

    this.minutesOptions = [
      { value: "00", text: '00' },
      { value: "30", text: '30' }
    ];

    this.periodOptions = [
      { value: "am", text: 'AM' },
      { value: "pm", text: 'PM' }
    ];
  }

  addItem() {
    console.log(" ======= Start Add Item ======= ");
    this.tryingToAdd = true;
    const startTime = moment(`${this.startHours}:${this.startMinutes} ${this.startPeriod}`, 'hh:mm A').utcOffset(0, true);
    const endTime = moment(`${this.endHours}:${this.endMinutes} ${this.endPeriod}`, 'hh:mm A').utcOffset(0, true);
    
    if(!startTime.isBefore(endTime)) {
      this.displayError = true;
      this.errorMessage = "Start Time Must Be Before End Time";
      this.tryingToAdd = false;
      return;
    }

    
    
    let index = this.findLocationForItem(startTime, endTime);

    if(index === -1 || index > 2) {
      //organize
      //index = this.findLocationForItem(startTime, endTime);
    }

    if(index === -1 || index > 2) {
      this.displayError = true;
      this.errorMessage = 'This item does not fit in the schedule';
      console.log("Doesn't fit");
      this.tryingToAdd = false;
      return;
    }
        
    console.log("It Fits! at ", index);
    const startDisplay = startTime.format('hh:mm A');
    const endDisplay = endTime.format('hh:mm A');
    console.log({startTime, endTime, startDisplay, endDisplay});
    // if(this.items[index]) {
      this.items[index].push({startTime, endTime, startDisplay, endDisplay});
      this.items[index] = this.items[index].sort((a: any, b: any) => a.startTime - b.startTime);
    // }
    // else {
    //   this.items[index] = [{startTime, endTime, startDisplay, endDisplay}];
    // }
    console.log(this.items.map((column: any) => { return column.map((obj: any) => { return {start: obj.startDisplay, end: obj.endDisplay} }); }))
    this.tryingToAdd = false;
    this.resetForm();

    console.log(" ======= End Add Item ======= \n");
  }

  findLocationForItem(startTime: moment.Moment, endTime: moment.Moment) {
    let result = -1;
    this.items.find((column: any, index: number) => {
      // Start time >= existing start time  && start time < existing end time OR
      // End time > existing start time && end time <= existing end time
      const fitsInColumn = (column.length === 0) || !(column).find((item: any) => {
        return ( item.startTime.isSameOrBefore(startTime) && startTime.isBefore(item.endTime) ) ||
          ( item.startTime.isBefore(endTime) && endTime.isSameOrBefore(item.endTime) )
      });


      if(fitsInColumn) {
        result = index;
      }

      return fitsInColumn;
    });

    return result;
  }

  resetForm() {
    this.startHours = "1";
    this.startMinutes = "00";
    this.startPeriod = "am";
    this.endHours = "2";
    this.endMinutes = "00";
    this.endPeriod = "am";
  }
  
}
</script>

<style scoped>

.date-container {
  display: block;
}

.center {
  text-align: center;
}



.collapse-button {
  width: 94%;
}

.collapse-content {
  width: 94%;
  margin-left: 3%;
  border: 1px solid #17a2b8;
}

.item-form {
  width: 94%;
  margin-left: 3%;
  margin-bottom: 2px;
}

.strike {
  text-decoration: line-through;
}

.btn-delete {
  font-size: xx-small;
}

.b-table-sticky-header {
  max-height: 70vh;
}

#home {
  background-color: #f7f8fc;
}

#date-input {
  font-weight: bold;
  font-size: large;
}

#main-content {
  max-width: 100%;
  width: 86%;
  margin-left: 7%;
  margin-top: 3%;
}

#to-do-button {
  margin-top: 6rem !important;
}

#table-container {
  max-height: 70vh;
  overflow-y: scroll;
}

</style>
