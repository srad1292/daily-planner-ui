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
      <b-row>
        <b-col cols="5"></b-col>
        <b-col cols="1">
          <button class="arrow-button"
          v-on:click.stop.prevent="deleteItem()"
          >
          Delete Item
          </button>
        </b-col>
      </b-row>
    </form>

    <b-container id="main-content">
      <div style="margin-top: 20px">
        <b-table-simple fixed sticky-header id="time-table">
          <b-thead head-variant="dark">
            <b-tr>
              <b-th>Time</b-th>
              <b-th v-for="(column, index) in tableData" v-bind:key="index + '-header-column'">
                <p class="table-cell">Column {{index+1}}</p>
              </b-th>
            </b-tr>
          </b-thead>
          <b-tbody>
            <b-tr v-for="(time, index) in times" v-bind:key="index + '-time-row'">
              <b-td>{{time.display}}</b-td>
              <template v-for="(column, rowIndex) in tableData">
                <template v-for="(item, itemIndex) in column">
                  <b-td 
                    v-if="item.startDisplay === time.display" 
                    :key="itemIndex + '-time-row-' + rowIndex" 
                    :rowspan="item.numCols"
                  >
                    <p class="table-cell">
                      {{item.startDisplay}} until {{item.endDisplay}}
                    </p>
                  </b-td>
                </template>
                
              </template>
            </b-tr>
          </b-tbody>
        </b-table-simple>
      </div>
    </b-container>


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
  times: any[] = [];
  items: any[];
  tableData: any[];
  hoursOptions: any[];
  minutesOptions: any[];
  periodOptions: any[];

  displayError: boolean;
  errorMessage: string;

  setupFlag: number = 1;

 

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
    this.tableData = [[{numCols: 96, hasData: false}],[{numCols: 96, hasData: false}],[{numCols: 96, hasData: false}]];
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
      { value: "15", text: '15' },
      { value: "30", text: '30' },
      { value: "45", text: '45' }
    ];

    this.periodOptions = [
      { value: "am", text: 'AM' },
      { value: "pm", text: 'PM' }
    ];

    let time = moment(`12:00 am`, 'hh:mm A').utcOffset(0, true);
    let endTime = moment(`11:45 pm`, 'hh:mm A').utcOffset(0, true);
    do {
      let display = time.format('hh:mm A');
      this.times.push({time, display});
      time = time.add(15, "minutes");
    } while(time.isSameOrBefore(endTime));
    
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

    console.log("diff");
    console.log(endTime.diff(startTime, 'minutes'));
    
    
    let index = this.findLocationForItem(this.items, startTime, endTime);
    console.log('index: ', index);
    if(index === -1 || index > 2) {
      const organized = this.organizeItems();
      index = this.findLocationForItem(organized, startTime, endTime);

      if(index === -1 || index > 2) {
        this.displayError = true;
        this.errorMessage = 'This item does not fit in the schedule';
        console.log("Doesn't fit");
        this.tryingToAdd = false;
        return;
      }

      this.items = organized.map((col: any[]) => col);
    }

    
        
    console.log("It Fits! at ", index);
    const startDisplay = startTime.format('hh:mm A');
    const endDisplay = endTime.format('hh:mm A');
    console.log({startTime, endTime, startDisplay, endDisplay});
    this.items[index].push({startTime, endTime, startDisplay, endDisplay});
    this.items[index] = this.items[index].sort((a: any, b: any) => a.startTime - b.startTime);

    console.log(this.items.map((column: any) => { return column.map((obj: any) => { return {start: obj.startDisplay, end: obj.endDisplay} }); }))
    this.tryingToAdd = false;
    this.createTable();
    // this.resetForm();

    console.log(" ======= End Add Item ======= \n");
  }

  createTable() {
    let tempTable: any[] = [[], [], []];
    const startOfDay = moment(`12:00 am`, 'hh:mm A').utcOffset(0, true);
    const lastRow = moment(`11:45 pm`, 'hh:mm A').utcOffset(0, true);
    const endOfDay = moment(`11:59 pm`, 'hh:mm A').utcOffset(0, true);
    this.items.forEach((column: any[], colIndex: number) => {
      if(column.length === 0) {
        tempTable[colIndex].push({numCols: 96, hasData: false, rowIndex: 0})
      }
      (column || []).forEach((item: any, itemIndex) => {
        let numOfCols: number = 1;
        let rowIndex: number = 0;
        if(itemIndex === 0 && item.startTime.isAfter(startOfDay)) {
          numOfCols = (item.startTime.diff(startOfDay, "minutes")) / 15;
          tempTable[colIndex].push({numCols: numOfCols, hasData: false, rowIndex});
        }
        else if(itemIndex > 0 && item.startTime.isAfter(column[itemIndex-1].endTime)) {
          numOfCols = (item.startTime.diff(column[itemIndex-1].endTime, "minutes")) / 15;
          rowIndex = this.times.findIndex((time: any) => time.display === column[itemIndex-1].endDisplay);
          tempTable[colIndex].push({numCols: numOfCols, hasData: false, rowIndex});
        }

        rowIndex = this.times.findIndex((time: any) => time.display === item.startDisplay);
        numOfCols = (item.endTime.diff(item.startTime, "minutes")) / 15;
        tempTable[colIndex].push({numCols: numOfCols, hasData: true, rowIndex, ...item});

        if(itemIndex === column.length-1 && item.endTime.isSameOrBefore(lastRow)) {
          numOfCols = (endOfDay.diff(item.endTime, "minutes")+1) / 15;
          rowIndex = this.times.findIndex((time: any) => time.display === item.endDisplay);
          tempTable[colIndex].push({numCols: numOfCols, hasData: false, rowIndex});
        }
      });
    });

    console.log("==== Table Data ====");
    this.tableData = tempTable;
    console.log(this.tableData);

  }

  findLocationForItem(items: any[], startTime: moment.Moment, endTime: moment.Moment) {
    let result = -1;
    items.find((column: any, index: number) => {
      // New Item Starts Before Existing Item But Ends After The Existing Item Starts OR
      // New Item Starts At The Same Time OR
      // New Item Starts After Existing Start But Before Existing End
      const fitsInColumn = (column.length === 0) || !(column).find((item: any) => {
        return (startTime.isBefore(item.startTime) && endTime.isAfter(item.startTime)) ||
          (startTime.isSame(item.startTime)) ||
          (startTime.isAfter(item.startTime) && startTime.isBefore(item.endTime))
      });


      if(fitsInColumn) {
        result = index;
      }

      return fitsInColumn;
    });

    return result;
  }

  deleteItem() {
    this.items[0].splice(1,1);
  }

  /**
   * Organizes the items by start time and then by length
   */
  organizeItems() {
    const sorted = [].concat(...this.items)
      .sort((timeA: any, timeB: any) => {
        if(timeA.startTime.isSame(timeB.startTime)) {
          return timeB.endTime.diff(timeB.startTime) - timeA.endTime.diff(timeA.startTime);
        }
        else {
          return timeA.startTime - timeB.startTime;
        }
      });
    
    console.table(sorted.map((item: any) => { return {start: item.startDisplay, end: item.endDisplay}} ));
    let organized: any[] = [[], [], []];

    sorted.forEach((item: any) => {
      console.log("Adding Item");
      console.log({start: item.startDisplay, end: item.endDisplay});
      const index = this.findLocationForItem(organized, item.startTime, item.endTime);
      console.log(`index ${index}`);
      organized[index].push(item);
    });

    console.log("==== Str Organized ====");
    console.log(organized);
    console.log("==== End Organized ====")
    return organized;
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
  max-height: 70vh;
  /* overflow-y: scroll; */
}

#to-do-button {
  margin-top: 6rem !important;
}

#table-container {
  max-height: 70vh;
  overflow-y: scroll;
}

#table-table {
  border-collapse: collapse;
}

#time-table th, #time-table td {
  border: 1px solid black;
}

#time-table td {
  height: 40px;
  width: 70px;
}

.table-cell {
  height: 30px !important;
  display: block;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
  margin-bottom: 0;
}

</style>
