<template>
  <div class="home">
    <div class="center date-container">
      <h1>Daily Planner</h1>
      <form>
        <button
          class="arrow-button"
          v-on:click.stop.prevent="updateDate('last')"
          :disabled="loading"
        >
          &lt;
        </button>
        <input
          name="view-date"
          id="date-input"
          type="date"
          v-model="pickerDate"
          v-on:input="loadScheduleAndTasks(pickerDate)"
          :disabled="loading"
          required
        />
        <button
          class="arrow-button"
          v-on:click.stop.prevent="updateDate('next')"
          :disabled="loading"
        >
          &gt;
        </button>
      </form>
    </div>

    <template v-if="loading">
      <div class="center">Loading for: {{ pickerDate }}</div>
    </template>

    <b-container id="main-content">
      <b-row>
        <b-col cols="3">
          <b-button
            v-b-toggle.todays-tasks
            variant="info"
            class="m-1 collapse-button"
            v-on:click.stop.prevent="updateDailyIcon()"
            >Today's Tasks {{ dailyIcon }}</b-button
          >

          <b-collapse id="todays-tasks" class="collapse-content">
            <ul>
              <li
                v-for="task in dailyTasks"
                v-bind:key="task.id"
                v-bind:class="{ strike: task.complete }"
              >
                <b-container>
                  <b-row>
                    <b-col cols="10">
                      <b-form-checkbox v-model="task.complete">
                        <p v-bind:class="{ strike: task.complete }">
                          {{ task.name }}
                        </p>
                      </b-form-checkbox>
                    </b-col>
                    <b-col cols="2">
                      <b-button
                        variant="danger"
                        class="btn-delete"
                        v-on:click.stop.prevent="deleteDailyTask(index)"
                        >X</b-button
                      >
                    </b-col>
                  </b-row>
                </b-container>
              </li>
            </ul>
            <form class="item-form">
              <b-input-group>
                <b-form-input
                  name="daily-task-input"
                  id="daily-task-input"
                  type="text"
                  v-model="newDailyTask"
                  :disabled="loading"
                ></b-form-input>
                <b-input-group-append>
                  <b-button
                    variant="success"
                    v-on:click.stop.prevent="addDailyTask()"
                    :disabled="newDailyTask.length === 0 || loading"
                    >Add Task</b-button
                  >
                </b-input-group-append>
              </b-input-group>
            </form>
          </b-collapse>

          <b-button
            v-b-toggle.to-do-list
            variant="info"
            class="m-1 collapse-button"
            id="to-do-button"
            v-on:click.stop.prevent="updateToDoIcon()"
            >To Do List {{ toDoIcon }}</b-button
          >

          <b-collapse id="to-do-list" class="collapse-content">
            <ul>
              <li v-for="(task, index) in toDoItems" v-bind:key="task.id">
                <b-container>
                  <b-row>
                    <b-col cols="10">
                      <b-form-checkbox v-model="task.complete">
                        <p v-bind:class="{ strike: task.complete }">
                          {{ task.name }}
                        </p>
                      </b-form-checkbox>
                    </b-col>
                    <b-col cols="2">
                      <b-button
                        variant="danger"
                        class="btn-delete"
                        v-on:click.stop.prevent="deleteToDoItem(index)"
                        >X</b-button
                      >
                    </b-col>
                  </b-row>
                </b-container>
              </li>
            </ul>
            <form class="item-form">
              <b-input-group>
                <b-form-input
                  name="to-do-input"
                  id="to-do-input"
                  type="text"
                  v-model="newToDoItem"
                  :disabled="loading"
                ></b-form-input>
                <b-input-group-append>
                  <b-button
                    variant="success"
                    v-on:click.stop.prevent="addToDoItem()"
                    :disabled="newToDoItem.length === 0 || loading"
                    >Add Task</b-button
                  >
                </b-input-group-append>
              </b-input-group>
            </form>
          </b-collapse>
        </b-col>

        <b-col></b-col>

        <b-col cols="8"> 
          <!-- <div id="table-container"> -->
          <b-table-simple sticky-header>
            <b-thead head-variant="dark">
              <b-tr>
                <b-th>Time</b-th>
                <b-th>Item One</b-th>
                <b-th>Item Two</b-th>
                <b-th>Item Three</b-th>
              </b-tr>
            </b-thead>
            <b-tbody>
              <b-tr>
                <b-td variant="dark">12:00</b-td>
                <b-td rowspan="25" style="background-color: #abcdef">Sleep</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">12:15</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">12:30</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">12:45</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">1:00</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">1:15</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">1:30</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">1:45</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">2:00</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">2:15</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">2:30</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">2:45</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">3:00</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">3:15</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">3:30</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">3:45</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">4:00</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">4:15</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">4:30</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">4:45</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">5:00</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">5:15</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">5:30</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">5:45</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">6:00</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">6:15</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">6:30</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">6:45</b-td>
                <b-td rowspan="3" style="background-color: #fcba03">Walk</b-td>
                <b-td rowspan="2" style="background-color: #ec6bfa">Think about all the things you have to do</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">7:00</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">7:15</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">7:30</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">7:45</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">8:00</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">8:15</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">8:30</b-td>
                <b-td rowspan="2" style="background-color: #d42269">Go To Work</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">8:45</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">9:00</b-td>
                <b-td rowspan="6" style="background-color: #ddefac">Figure out mocha and chai</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">9:15</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">9:30</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">9:45</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">10:00</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">10:15</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">10:30</b-td>
                <b-td rowspan="2" style="background-color: #fabfac">Document Findings</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">10:45</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">11:00</b-td>
                <b-td style="background-color: #dccbba">Take a quick break</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">11:15</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">11:30</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">11:45</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">12:00</b-td>
                <b-td style="background-color: #3bd3f5">API Daily Standup with Uday, Keerthi, and Charan</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">12:15</b-td>
                <b-td rowspan="4" style="background-color: #bbcaab">Lunch</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">12:30</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">12:45</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">1:00</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">1:15</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">1:30</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">1:45</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">2:00</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">2:15</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">2:30</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">2:45</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">3:00</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">3:15</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">3:30</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">3:45</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">4:00</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">4:15</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">4:30</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">4:45</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">5:00</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">5:15</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">5:30</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">5:45</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">6:00</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">6:15</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">6:30</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">6:45</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">7:00</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">7:15</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">7:30</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">7:45</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">8:00</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">8:15</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">8:30</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">8:45</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">9:00</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">9:15</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">9:30</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">9:45</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">10:00</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">10:15</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">10:30</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">10:45</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">11:00</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">11:15</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">11:30</b-td>
              </b-tr>
              <b-tr>
                <b-td variant="dark">11:45</b-td>
              </b-tr>
            </b-tbody>
          </b-table-simple>
          <!-- </div> -->
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import { TaskItem } from "../models/task-item.interface";
import moment from "moment";

@Component
export default class Home extends Vue {
  pickerDate: string;
  loading: boolean = false;
  dailyIcon: string;
  dailyTasks: TaskItem[];
  newDailyTask: string;
  toDoIcon: string;
  toDoItems: TaskItem[];
  newToDoItem: string;

  constructor() {
    super();
    this.pickerDate = moment().format("YYYY-MM-DD");
    this.dailyIcon = "+";
    this.dailyTasks = [
      {
        name: "Keep messing with schedule feature",
        complete: false
      },
      {
        name:
          "Allow for these items to be updated",
        complete: false
      },
      {
        name: "Figure out start and end dates for HA-RE, HA-PA, and HA-BA",
        complete: false
      },
      {
        name: "Stare into the abyss",
        complete: true
      }
    ];
    this.newDailyTask = "";
    this.toDoIcon = "+";
    this.toDoItems = [
      {
        name:
          "Learn about unit testing oh man this is a pretty long task name wowowow this is long",
        complete: true
      },
      {
        name: "Learn about building project into dist folder",
        complete: false
      }
    ];
    this.newToDoItem = "";
  }

  updateDailyIcon() {
    this.dailyIcon = this.dailyIcon === "+" ? "-" : "+";
  }

  addDailyTask() {
    if (this.newDailyTask.length > 0) {
      this.loading = true;
      let delay = (time: number) => (result: any) =>
        new Promise(resolve => setTimeout(() => resolve(result), time));
      Promise.resolve("Data")
        .then(delay(1500))
        .then(result => {
          this.dailyTasks.push({
            name: this.newDailyTask,
            complete: false
          });
          this.newDailyTask = "";
          this.loading = false;
        });
    }
  }

  deleteDailyTask(index: number) {
    console.log(index);
    this.loading = true;
    let delay = (time: number) => (result: any) =>
      new Promise(resolve => setTimeout(() => resolve(result), time));
    Promise.resolve("Data")
      .then(delay(1000))
      .then(result => {
        this.dailyTasks.splice(index, 1);
        this.loading = false;
      });
  }

  updateToDoIcon() {
    this.toDoIcon = this.toDoIcon === "+" ? "-" : "+";
  }

  addToDoItem() {
    if (this.newToDoItem.length > 0) {
      this.loading = true;
      let delay = (time: number) => (result: any) =>
        new Promise(resolve => setTimeout(() => resolve(result), time));
      Promise.resolve("Data")
        .then(delay(0))
        .then(result => {
          this.toDoItems.push({
            name: this.newToDoItem,
            complete: false
          });
          this.newToDoItem = "";
          this.loading = false;
        });
    }
  }

  deleteToDoItem(index: number) {
    console.log(index);
    this.loading = true;
    let delay = (time: number) => (result: any) =>
      new Promise(resolve => setTimeout(() => resolve(result), time));
    Promise.resolve("Data")
      .then(delay(1000))
      .then(result => {
        this.toDoItems.splice(index, 1);
        this.loading = false;
      });
  }

  updateDate(direction: string) {
    let newDate;
    if (direction === "last") {
      newDate = moment(this.pickerDate).subtract(1, "days");
    } else {
      newDate = moment(this.pickerDate).add(1, "days");
    }
    this.pickerDate = newDate.format("YYYY-MM-DD");
    this.loadScheduleAndTasks(newDate.format("YYYY-MM-DD"));
  }

  loadScheduleAndTasks(date: string) {
    this.loading = true;
    let delay = (time: number) => (result: any) =>
      new Promise(resolve => setTimeout(() => resolve(result), time));

    Promise.resolve("Data")
      .then(delay(1500))
      .then(result => (this.loading = false));
  }

  getColors() {
    let colors = [];
    ([]).forEach(label => colors.push('#'+Math.floor(Math.random()*16777215).toString(16)));
  }
}
</script>

<style scoped>
ul {
  text-align: left;
  list-style-type: none;
  padding-top: 3px;
  padding-left: 5%;
}

.date-container {
  display: block;
}

.center {
  text-align: center;
}

.arrow-button {
  margin-left: 3px;
  margin-right: 3px;
  font-size: large;
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
