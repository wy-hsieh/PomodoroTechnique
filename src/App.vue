<template>
  <div id="app">
    <b-container>
      <b-row>
        <b-col cols="12">
          <b-btn variant="success" @click="pause" v-if="status === 'counting'">
            <font-awesome-icon :icon="['fas', 'pause']" />
          </b-btn>
          <b-btn variant="success" @click="play" v-else>
            <font-awesome-icon :icon="['fas', 'play']" />
          </b-btn>
          {{ inprogress === '' ? "沒有事情" : inprogress }}
          -
          {{ timetext }}
          <hr>
        </b-col>
        <b-col cols="12">
          <b-form-group
            id="input-group-1"
            label="新增代辦事項"
            label-for="input-1"
          >
            <b-form-input
              id="input-1"
              v-model="newtodo"
              type="text"
              @keydown.enter="add"
            ></b-form-input>
          </b-form-group>
          <ol>
            <li v-for="(todo, index) in todos" :key="index">
              {{ todo }}
              <input type="button" value="x" @click="del('todos', index)">
            </li>
          </ol>
          <hr>
        </b-col>
        <b-col cols="12">
          <h1>已完成</h1>
          <ol>
            <li v-for="(f, index) in finished" :key="index">
              {{ f }}
              <input type="button" value="x" @click="del('finished', index)">
            </li>
          </ol>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
export default {
  name: 'app',
  data () {
    return {
      todos: [],
      finished: [],
      newtodo: '',
      inprogress: '',
      timeleft: 5,
      status: '',
      timer: 0
    }
  },
  computed: {
    timetext () {
      let m = Math.floor(this.timeleft / 60)
      let s = Math.floor(this.timeleft % 60)
      return `${m} 分 ${s} 秒`
    }
  },
  methods: {
    add () {
      this.todos.push(this.newtodo)
      this.newtodo = ''
    },
    del (array, index) {
      if (array === 'todos') {
        this.todos.splice(index, 1)
      } else if (array === 'finished') {
        this.finished.splice(index, 1)
      }
    },
    play () {
      if (this.status === 'pause') {
        this.status = 'counting'
        this.timer = setInterval(() => {
          this.timeleft--
          if (this.timeleft < 0) {
            this.finish()
          }
        }, 1000)
      } else {
        if (this.todos.length > 0) {
          this.inprogress = this.todos[0]
          this.todos.splice(0, 1)
          this.status = 'counting'
          this.timer = setInterval(() => {
            this.timeleft--
            if (this.timeleft < 0) {
              this.finish()
            }
          }, 1000)
        } else {
          alert('沒有東西')
        }
      }
    },
    finish () {
      clearInterval(this.timer)
      this.finished.push(this.inprogress)
      this.status = ''
      this.timeleft = 5
      this.inprogress = ''
      if (this.todos.length > 0) {
        this.play()
      } else {
        alert('結束')
      }
    },
    pause () {
      clearInterval(this.timer)
      this.status = 'pause'
    }
  }
}
</script>
