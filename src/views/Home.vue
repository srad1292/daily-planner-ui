<template>
  <div class="home">
    <div class="center date-container">
      <h1>Daily Planner</h1>
      <form>
        <button class="arrow-button" v-on:click.stop.prevent="updateDate('last')" :disabled="loading">&lt;</button>
        <input name="view-date" id="date-input" type="date" v-model="pickerDate" v-on:input="loadScheduleAndTasks(pickerDate)" :disabled="loading" required> 
        <button class="arrow-button" v-on:click.stop.prevent="updateDate('next')" :disabled="loading">&gt;</button>
      </form>
    </div>

    <template v-if="loading">
      <div class="center">
        Loading for: {{pickerDate}}
      </div>
    </template>
  </div>
</template>

<script lang="ts">

import { Component, Prop, Vue } from 'vue-property-decorator';
import moment from "moment";

@Component
export default class Home extends Vue {
  pickerDate: string;
  loading: boolean = false;

  constructor() {
    super();
    this.pickerDate = "2019-12-22";
  }

  updateDate(direction: string) {
    let newDate;
    if(direction === "last") {
      newDate = moment(this.pickerDate).subtract(1, 'days');
    }
    else {
      newDate = moment(this.pickerDate).add(1, 'days');
    }
    this.pickerDate = newDate.format("YYYY-MM-DD");
    this.loadScheduleAndTasks(newDate.format("YYYY-MM-DD"));
  }

  loadScheduleAndTasks(date: string) {
    this.loading = true;
    let delay = (time: number) => (result: any) => new Promise(resolve => setTimeout(() => resolve(result), time));

    Promise.resolve("Data")
      .then(delay(1500))
      .then(result => this.loading = false);
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

.arrow-button {
  margin-left: 3px;
  margin-right: 3px;
  font-size: large;
}

#date-input {
  font-weight: bold;
  font-size: large;
}

</style>
