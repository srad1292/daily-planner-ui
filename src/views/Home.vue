<template>
  <div class="home">
    <div class="center">
      <h1>12-22-2019</h1>
      <!-- <form #dateForm="ngForm">
        <input name="view-date" type="date" [(ngModel)]="pickerDate" required> 
        <button class="btn btn-primary" [disabled]="loadingActivities || deletingActivity || !dateForm.form.valid" (click)="changeDate()">Go To Date</button>
      </form> -->
    </div>
  </div>
</template>

<script lang="ts">

import { Component, Prop, Vue } from 'vue-property-decorator';

@Component
export default class Home extends Vue {
  header: string;

  constructor() {
    super();
    this.header = "Welcome to a single recipe view";
  }
}
</script>

<style scoped>
.center {
  text-align: center;
}
</style>
