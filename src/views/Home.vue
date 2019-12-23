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
              <li v-for="task in dailyTasks" v-bind:key="task.id">
                {{ task }}
              </li>
            </ul>
            <form id="daily-task-form">
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
        </b-col>

        <b-col></b-col>

        <b-col cols="8"> </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import moment from "moment";

@Component
export default class Home extends Vue {
  pickerDate: string;
  loading: boolean = false;
  dailyIcon: string;
  dailyTasks: string[];
  newDailyTask: string;

  constructor() {
    super();
    this.pickerDate = moment().format("YYYY-MM-DD");
    this.dailyIcon = "+";
    this.dailyTasks = ["Daily Task Feature", "To Do List", "Get Taco Bell"];
    this.newDailyTask = "";
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
          this.dailyTasks.push(this.newDailyTask);
          this.newDailyTask = "";
          this.loading = false;
        });
    }
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
}
</script>

<style scoped>
ul {
  text-align: left;
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

#daily-task-form {
  width: 94%;
  margin-left: 3%;
  margin-bottom: 2px;
}
</style>
