

<template>
    <AddTask v-show="showAddTask" @add-task="addTask" />
    <Tasks @toggle-reminder="toggleReminder" 
    @delete-task="deleteTask" :tasks="tasks" />
</template>


<script>
import Tasks from '../components/Tasks'
import AddTask from '../components/AddTask'

export default {
    name: 'Home',
    props: {
        showAddTask: Boolean,
    },
    components: {
        Tasks,
        AddTask
    },
    data() {
        return{
        tasks: [],
        }
    },
     methods:{
 
    //added new input task to json
    async addTask(task){
      const res = await fetch('api/tasks', {
        method: 'POST',
        headers: {
          'Content-type' : 'application/json',
        },
        body: JSON.stringify(task),
      })
      const data = await res.json()

      this.tasks = [...this.tasks, data]
    },
    //delete task from json
    async deleteTask(id){
      if (confirm('Are you sure?')){
          const res = await fetch(`api/tasks/${id}`, {
            method: 'DELETE'
          })
        res.status === 200 ? (this.tasks = this.tasks.filter((task) => task.id !== id) ): alert('Eror deleting task')
      }
    },
    //toggle the actual reminder in a tasks
   async toggleReminder(id){
     const taskToToggle = await this.fetchTasks(id)
     const updTask = {...taskToToggle, reminder: !taskToToggle.reminder}

     const res = await fetch(`api/tasks/${id}`, {
       method: 'PUT',
       headers: {
         'Content-type' : 'application/json'
       },
       body: JSON.stringify(updTask)
     })
      const data = await res.json()
      //if task.id equals to the id that passed in, change the class reminder to the opposite of current task
       this.tasks = this.tasks.map((task) => task.id === id ? {...task, reminder: data.reminder} : task
       )
    },
    //fetch all the tasks
    async fetchTasks(){
      const res = await fetch('api/tasks')
      const data = await res.json()
      return data
    },
    //fetch a single task
    async fetchTask(id){
      const res = await fetch(`api/tasks/${id}`)
      const data = await res.json()
      return data
    }
  },
  async created(){
    this.tasks = await this.fetchTasks()
  }
}
</script>


