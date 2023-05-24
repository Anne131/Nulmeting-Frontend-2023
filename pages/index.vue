<template>
  <client-only placeholder="loading...">
    <div class="page-wrapper">
    <div class="element-wrapper">
      <div class="element-body">
        <h1>Welkom bij de Levarne Nulmeting</h1>
        <LevButton @clicked="createToDo"> Get To Do </LevButton>
        <br>
        <br>
        <div id="text"> The to do is already in the table! </div>
        <br>
        <table id="todos">
          <tr>
            <th>Assignee</th>
            <th>description</th>
            <th>Deadline</th>
          </tr>
        </table>
      </div>
    </div>
  </div>
  </client-only>
</template>

<script setup lang="ts">
import axios from 'axios'
import { table } from 'console';
import { defineStore } from 'pinia'

const useTodoStore = defineStore('alerts', {
  state: () => ({id: "", assignee: "", deadline: "", description: ""}),
  actions: {
    todos(){
    let config = {
      method: 'get',
      maxBodyLength: Infinity,
      url: 'https://86a4h9y007.execute-api.eu-west-1.amazonaws.com/development/nulmeting/todo',
      headers: { 
        'x-api-key': 'zHUsT9DYvS69RTkaji9lwS7oEX9ddpqaqSbBlFZ4'
      }
    };
    axios.request(config)
    .then((response) => {
      let todo = response.data.todo; 
      this.id = todo.id
      this.assignee = todo.assignee
      let date = todo.dueDateTime
      let s = date.split('T')
      let time = s[1].split('.')
      this.deadline = s[0] + " " + time[0]
      this.description = todo.description
    })
    .catch((error) => {
      console.log(error);
    });
  }
  }
})


const todoStore = useTodoStore();
const {todos} = todoStore

let ids = [] as string[]
function createToDo(){
  todos()
  if (!ids.includes(todoStore.id) && todoStore.id != ""){
    const tab = document.getElementById("todos")
    const tr = document.createElement("tr")
    const ass = document.createElement("td")
    const des = document.createElement("td")
    const line = document.createElement("td")
    ass.innerHTML = todoStore.assignee
    des.innerHTML = todoStore.description
    line.innerHTML = todoStore.deadline
    ids.push(todoStore.id)
    tr.appendChild(ass)
    tr.appendChild(des)
    tr.appendChild(line)
    tab?.appendChild(tr)
    let er = document.getElementById("text")
    if (er != null) er.style.visibility = "hidden"
  }
  else if(ids.includes(todoStore.id)) {
    let er = document.getElementById("text")
    if (er != null) er.style.visibility = "visible"
  }
}

</script>

<style>
#text {
  visibility: hidden;
}

#todos {
  font-family: Arial, Helvetica, sans-serif;
  border-collapse: collapse;
  width: 100%;
}

#todos td, #todos th {
  border: 1px solid #ddd;
  padding: 8px;
}

#todos tr:nth-child(even){background-color: #f2f2f2;}

#todos tr:hover {background-color: #ddd;}

#todos th {
  padding-top: 12px;
  padding-bottom: 12px;
  text-align: left;
  background-color: #175dc7;
  color: white;
}
</style>