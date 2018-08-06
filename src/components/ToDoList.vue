<template>
<div class="container">
  <div id="app" class="col-sm-10 col-sm-offset-1">
    <br>
    <div class="panel panel-success">
      <div class="panel-heading">
        <h3 class="text-center">Admin Panel</h3>
      </div>
      <div class="panel-body">
        <div class="col-sm-6">
          <label for="">Display title</label>
          <input type="checkbox" v-model="displayTitle">
          <br />
          <label for="">Display add new task</label>
          <input type="checkbox" v-model="displayAddTasks">
          <br />
          <label for="">Display task stats</label>
          <input type="checkbox" v-model="displayTaskStatistics">
        </div>
        <div class="col-sm-6">
          <label for="">Display tasks</label>
          <input type="checkbox" v-model="displayTasks">
          <br />
          <label for="">Display task completion bar progress</label>
          <input type="checkbox" v-model="displayProgressBar">
        </div>
      </div>
    </div>
    <br>
    <h1 v-if="displayTitle">To Do List</h1>
    <div class="panel panel-info" v-if="displayAddTasks" >
      <div class="panel-heading">
        <h3 class="text-center">Add New Task</h3>
      </div>
      <div class="panel-body">
        <form v-on:submit="addTask">
          <div class="col-sm-8">
            <input type="text" class="form-control" v-model="tasks.name">
          </div>
          <div class="col-sm-4">
            <input type="submit" class="btn btn-primary btn-block" value="Add">
          </div>
        </form>
      </div>  
    </div> 
    <div class="panel panel-success" v-if="displayProgressBar" >
      <div class="panel-heading">
        <h3 class="text-center">Completion Progress Bar</h3>
      </div>
      <div class="panel-body">
        <div class="col-sm-8 col-sm-offset-2">
          <div class="CompletionProgressGreyBar">
            <div class="CompletionProgressGreenBar text-center" :style="{ width: percentageofTasksCompleted + '%' }">
              {{ Math.round(percentageofTasksCompleted) }}%
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="panel panel-info" v-if="displayTaskStatistics" style="height: 230px;" >
      <div class="panel-heading">
        <h3 class="text-center">Task Statistics</h3>  
      </div>
      <div class="panel-body col-sm-12">
        <div class="col-sm-6">
          <div class="default"><span class="label label-default label-large" v-on:mouseover="changeTotalTasks">Total Tasks:  {{ tasks.length }} </span></div>
          <div class="primary"><span class="label label-primary label-large" v-on:mouseover="changeLeftToDo">Tasks Left To Do: {{ leftToDo }}</span></div>
           <div class="success"><span class="label label-success label-large" v-on:mouseover="changeCheckMarked">Check Marked Tasks: {{ checkMarkedTasks }}</span></div>
          <div class="danger"><span class="label label-danger label-large" v-on:mouseover="changeDeleted">Deleted Tasks: {{ this.deletedTasks }}</span></div>
        </div>
        <div class="col-sm-6">
          <h3>{{ displayedTasksStatView }}</h3>
          <h3 class="text-center" style="padding: 10px;" v-bind:class="manageable" >{{ leftToDo <= 5 ? 'Manageable'  : leftToDo > 5 && leftToDo < 10 ? 'Get to work' : 'Tasks Overload'}}</h3>
        </div>
      </div>
    </div>
    <table class="table" v-if="displayTasks && tasks.length > 0">
          <thead>
            <th>Task name</th>
            <th style="text-align: center;">Check when is done</th>
            <th style="text-align: center;">Delete</th>
          </thead>
          <tbody>
            <tr v-for="task in tasks">
              <td><span :class="{ taskDone: task.done }">{{ task.name }}</span></td>
              <td style="text-align: center; vertical-align: middle;"><input id="check" type="checkbox" v-model="task.done"></td>
              <td><button class="btn btn-danger btn-block" v-on:click="deleteTask(task)">Delete</button></td>
            </tr>
          </tbody>
        </table>
        <h3 class="text-center" v-else>The task list has been hidden or there are no task to display</h3>
  </div>
</div>
</template>

<script>
  export default {
    name: 'ToDoListApp',
    data() {
      return {
        displayTitle: true,
        displayTasks: true,
        displayProgressBar: false,
        displayAddTasks: true,
        displayTaskStatistics: false,
        displayedTasksStat: 'totalTasks',
        deletedTasks: 0,
        tasks: []
      }
    },
    methods: {
      addTask: function(event){
        event.preventDefault();

        if (this.tasks.name !== '' && this.tasks.name !== undefined && this.tasks.name.trim() !== '') {
          this.tasks.push({
            name: this.tasks.name,
            done: false
          });
        }
      },
      deleteTask: function(task) {
          this.tasks.splice(this.tasks.indexOf(task), 1);
          this.deletedTasks++;
        
      },
      changeTotalTasks: function() {
        this.displayedTasksStat = 'totalTasks';
      },
      changeLeftToDo: function() {
        this.displayedTasksStat = 'leftToDo';
      },
      changeCheckMarked: function() {
        this.displayedTasksStat = 'checkMarked';
      },
      changeDeleted: function() {
        this.displayedTasksStat = 'deletedTasks';
      }
    },
    computed: {
      checkMarkedTasks: function() {
        let count = 0;
        for (let i = 0; i < this.tasks.length; ++i) {
          if(this.tasks[i].done == true) {
            count ++;
          }
        }
        return count;
      },
      leftToDo: function() {
        return this.tasks.length - this.checkMarkedTasks;
      },
       displayedTasksStatView: function() {
        if (this.displayedTasksStat == 'totalTasks') {
          return 'Total Tasks: ' + this.tasks.length;
        } else if (this.displayedTasksStat == 'leftToDo') {
          return 'Tasks Left: ' + this.leftToDo;
        } else if (this.displayedTasksStat == 'checkMarked') {
          return 'Check Marked Tasks: ' + this.checkMarkedTasks;
        } else if (this.displayedTasksStat == 'deletedTasks') {
          return 'Deleted Tasks: ' + this.deletedTasks;
        }
      },
      manageable: function() {
        if( this.leftToDo <= 5) {
          return 'green';
        } else if (this.leftToDo > 5 && this.leftToDo < 10) {
          return 'yellow';
        } else {
          return 'red';
        }
      },
      percentageofTasksCompleted: function() {
        if (this.tasks.length == 0) {
          return 0;
        } else {
          return (this.checkMarkedTasks / this.tasks.length) * 100;
        }
      }
    }
  }
</script>
