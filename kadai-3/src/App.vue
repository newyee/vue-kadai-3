<template>
  <div id="app">
    <h1>ToDoリスト</h1>
    <input type="radio" name="state" id="check_value_left"  value="すべて" checked @click="displayData">すべて
    <input type="radio" name="state" id="check_value_middle" value="作業中" @click="getWorkingStatus">作業中
    <input type="radio" name="state" id="check_value_right" value="完了" @click="getCompleteStatus">完了
    <table>
      <thead>
      <th>ID</th>
      <th>コメント</th>
      <th>状態</th>
      </thead>
      <tbody id="task_list">
      </tbody>
    </table>
    <h2>新規タスクの追加</h2>
    <input type="text" id="input-form" v-model="inputData">
    <input type="button" value="追加" class="input-btn" @click="addTask">
  </div>
</template>

<script>
export default {
  data() {
    return {
      inputData: '',
      tbody: '',
      todos: []
    }
  },
  mounted() {
    this.tbody = document.getElementById('task_list')
  },
  methods: {
    addTask() {
      if (this.inputData) {
        const todo = {
          task: this.inputData,
          status: '作業中'
        }
        this.todos.push(todo)
        const workingStatus = document.getElementById('check_value_middle').checked
        const completeStatus = document.getElementById('check_value_right').checked
        if (workingStatus) {
          this.getWorkingStatus()
        } else if (completeStatus) {
          this.getCompleteStatus()
        } else {
          this.displayData()
        }
        this.inputData = ''
      }
    },
    deleteRow() {
      while (this.tbody.rows[0]) this.tbody.deleteRow(0)
    },
    getCompleteStatus() {
      this.deleteRow()
      for (let i = 0; i < this.todos.length; ++i) {
        if (this.todos[i].status !== '完了') {
          continue
        }
        this.createRow(i)
      }
    },
    getWorkingStatus() {
      this.deleteRow()
      for (let i = 0; i < this.todos.length; ++i) {
        if (this.todos[i].status !== '作業中') {
          continue
        }
        this.createRow(i)
      }
    },
    displayData() {
      this.deleteRow()
      this.todos.forEach((_, index) => {
        this.createRow(index)
      })
    },
    createStateButton(index) {
      const stateButton = document.createElement('button')
      stateButton.setAttribute('value', 'working')
      stateButton.textContent = this.todos[index].status
      stateButton.addEventListener('click', () => {
        const leftRadioButton = document.getElementById('check_value_left').checked
        if (leftRadioButton && this.todos[index].status === '作業中') {
          this.todos[index].status = '完了'
          this.displayData()
          return
        }
        if (leftRadioButton && this.todos[index].status === '完了') {
          this.todos[index].status = '作業中'
          this.displayData()
          return
        }
        if (this.todos[index].status === '作業中') {
          this.todos[index].status = '完了'
          this.getWorkingStatus()
        } else if (this.todos[index].status === '完了') {
          this.todos[index].status = '作業中'
          this.getCompleteStatus()
        }
      })
      return stateButton
    },
    createDeleteButton(index) {
      const deleteButton = document.createElement('button')
      deleteButton.addEventListener('click', () => {
        const rightRadioButton = document.getElementById('check_value_right').checked
        const middleRadioButton = document.getElementById('check_value_middle').checked
        this.todos.splice(index, 1)
        if (rightRadioButton) {
          this.getCompleteStatus()
        } else if (middleRadioButton) {
          this.getWorkingStatus()
        } else {
          this.displayData()
        }
      })
      deleteButton.textContent = '削除'
      return deleteButton
    },
    createRow(i) {
      const row = this.tbody.insertRow(-1)
      const tdId = row.insertCell()
      const tdComment = row.insertCell()
      const tdStateButton = row.insertCell()
      const tdDeleteButton = row.insertCell()
      const stateButton = this.createStateButton(i)
      const deleteButton = this.createDeleteButton(i)
      tdId.textContent = i
      tdComment.textContent = this.todos[i].task
      tdStateButton.appendChild(stateButton)
      tdDeleteButton.appendChild(deleteButton)
    }
  }
}
</script>
<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
table{
  margin:0 auto;
}
input{
  margin:0.3rem;
}
.input-btn{
  padding: 0;
}
</style>
