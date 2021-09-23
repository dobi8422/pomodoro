<template>
  <div id="MainBlock" class="row d-flex justify-content-center" :class="theme_background">
    <!-- operator -->
    <div class="col-11 col-sm-10 col-md-8 col-lg-6 col-xl-4 col-xxl-3 mt-3">
      <div class="input d-flex justify-content-between rounded-pill" :class="theme_bg_color1">
        <button class="btn input_addtodo rounded-circle" :class="theme_bg_color1" @click="add_todo"><i class="fas fa-plus"></i></button>
        <input class="col form-control border-0" :class="theme_bg_color1" type="input_text" v-model="input_todo" @keyup.enter="add_todo" required="required"/>
        <button class="btn input_edit rounded-circle" :class="theme_bg_color1" data-bs-toggle="modal" data-bs-target="#todolistModal" @click="label='undone', quick_delete=false"><i class="fas fa-stream"></i></button>
        <button class="btn rounded-circle" :class="theme_bg_color1" title="edit" data-bs-toggle="modal" data-bs-target="#settingModal" @click="label='time_set'"><i class="fas fa-cog"></i></button>
      </div>
      <!-- {{  }}裡面沒辦法放(小於, < )??? -->
      <div class="timing text-light">{{ 10>now_time[0] ? `0${now_time[0]}`: now_time[0] }}:{{ 10>now_time[1] ? `0${now_time[1]}`: now_time[1] }}</div>
      <div class="function">
        <button class="btn rounded-circle" :class="theme_bg_color1" @click="now_notice = notice_sound_record" v-if="!now_notice" title="notice"><i class="fas fa-bell"></i></button>
        <button class="btn rounded-circle" :class="theme_bg_color2" @click="now_notice = '', $refs.notice.pause()" v-else title="notice"><i class="fas fa-bell"></i></button>
        <button class="btn rounded-circle text-warning mx-3" :class="theme_bg_color2" @click="timing_start" v-if="!start || pause " title="start"><i class="fas fa-play"></i></button>
        <button class="btn rounded-circle text-danger mx-3" :class="theme_bg_color2" @click="timing_pause" v-else title="pasue"><i class="fas fa-pause"></i></button>
        <button class="btn rounded-circle" :class="theme_bg_color1" @click="timing_restart" v-if="start || pause" title="restart"><i class="fas fa-undo-alt"></i></button>
        <button class="btn rounded-circle opacity-0" v-else title="restart"><i class="fas fa-undo-alt"></i></button>
      </div>
      <audio ref="audio" style="height:22px">
        <source :src="now_music.src"/>
      </audio>
      <audio ref="notice" style="height:22px">
        <source :src="now_notice.src"/>
      </audio>
      <div class="mt-4 rounded-pill w-75 mx-auto d-flex justify-content-between" :class="theme_bg_color1" v-if="doingName"><div class="text-light m-auto text-break">{{ doingName }}</div><button class="btn rounded-circle text-light" @click="this.doingName = ''"><i class="fas fa-times"></i></button></div>
    </div>
    <!-- setting -->
    <div class="modal fade" id="settingModal" tabindex="-1" aria-labelledby="settingModalLabel" aria-hidden="true">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="settingModalLabel"><i class="fas fa-cog"></i>&ensp;設定</h5>
            <button type="button" class="btn btn-secondary position-absolute end-0 me-3" v-if="label == 'music_set' && now_music" @click="now_music = ''">關閉音樂</button>
            <button type="button" class="btn btn-secondary position-absolute end-0 me-3" v-if="label == 'notice_set' && now_notice" @click="now_notice = ''">關閉音效</button>
          </div>
          <div class="modal-body">
            <ul class="nav nav-tabs border-0">
              <li class="nav-item">
                <a class="nav-link text-dark" :class="{'active':label =='time_set'}" @click="label = 'time_set'" aria-current="page" href="#">時間</a>
              </li>
              <li class="nav-item">
                <a class="nav-link text-dark" :class="{'active':label =='music_set'}" @click="label = 'music_set'" aria-current="page" href="#">音樂</a>
              </li>
              <li class="nav-item">
                <a class="nav-link text-dark" :class="{'active':label =='notice_set'}" @click="label = 'notice_set'" aria-current="page" href="#">提醒音效</a>
              </li>
              <li class="nav-item position-absolute end-0 me-3">
                <a class="nav-link text-dark" :class="{'active':label =='theme_set'}" @click="label = 'theme_set'" aria-current="page" href="#">主題</a>
              </li>
            </ul>
            <ul class="list-group" v-if="label == 'time_set'">
            <li class="list-group-item d-flex justify-content-center">番茄時間&emsp;
              <input class="text-center" type="number" style="width:40px;" @change="change_time" v-model="setting_time[0][0]">：<input class="text-center" type="number" style="width:40px;" @change="change_time" v-model="setting_time[0][1]">
            </li>
            <li class="list-group-item d-flex justify-content-center align-items-center">休息時間&emsp;
              <input class="text-center" type="number" style="width:40px;" @change="change_time" v-model="setting_time[1][0]">：<input class="text-center" type="number" style="width:40px;" @change="change_time" v-model="setting_time[1][1]">
            </li>
            </ul>
            <ul class="list-group" v-if="label == 'music_set'">
              <li class="list-group-item d-flex justify-content-between align-items-center" :class="{[theme_bg_color2]:item.name === now_music.name}" @click="now_music=item, $refs.audio.load()" v-for="item in music" :key="item">
                {{ item.name }}
                <audio controls style="height:22px">
                  <source :src="item.src"/>
                </audio>
              </li>
            </ul>
            <ul class="list-group" v-if="label == 'notice_set'">
              <li class="list-group-item d-flex justify-content-between align-items-center" :class="{[theme_bg_color2]:item.name === now_notice.name}" @click="now_notice=item, notice_sound_record = item, $refs.notice.load()" v-for="item in notice_sound" :key="item">
                {{ item.name }}
                <audio controls style="height:22px">
                  <source :src="item.src"/>
                </audio>
              </li>
            </ul>
            <ul class="list-group" v-if="label == 'theme_set'">
              <li class="list-group-item d-flex justify-content-between align-items-center" :class="{[theme_bg_color2]:item.name === now_theme.name}" @click="now_theme=item" v-for="item in theme" :key="item">
                {{ item.name }}
            </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
    <!-- todolist -->
    <div class="modal fade" id="todolistModal" tabindex="-1" aria-labelledby="todolistModalLabel" aria-hidden="true">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="todolistModalLabel"><i class="fas fa-stream"></i>&ensp;清單</h5>
            <button type="button" class="btn position-absolute end-0 me-3" :class="{'btn-secondary' : !quick_delete,'btn-danger' : quick_delete}" v-if="label == 'undone'" @click="quick_delete_mode">快速刪除模式</button>
            <button type="button" class="btn btn-secondary position-absolute end-0 me-3" data-bs-dismiss="modal" v-if="label == 'completed'" @click="delete_all_completed">清除所有紀錄</button>
            <button type="button" class="btn btn-secondary position-absolute end-0 me-3" data-bs-dismiss="modal" v-if="label == 'deleted'" @click="delete_all_deleted">清除所有紀錄</button>
          </div>
          <div class="modal-body">
            <ul class="nav nav-tabs border-0">
              <li class="nav-item">
                <a class="nav-link text-dark" :class="{'active':label =='undone'}" @click="label = 'undone'" aria-current="page" href="#">未完成</a>
              </li>
              <li class="nav-item">
                <a class="nav-link text-dark" :class="{'active':label =='completed'}" @click="label = 'completed', quick_delete=false" aria-current="page" href="#">已完成</a>
              </li>
              <li class="nav-item position-absolute end-0 me-3">
                <a class="nav-link text-dark" :class="{'active':label =='deleted'}" @click="label = 'deleted', quick_delete=false" aria-current="page" href="#">刪除清單</a>
              </li>
            </ul>

            <ul class="list-group" v-if="label == 'undone'">
              <li class="list-group-item d-flex justify-content-between" :class="{[theme_bg_color2]:item.content === doingName}" @click="change_todo(item)" v-for="item in todo_list" :key="item">
                <div class="w-75 d-flex justify-content-start">
                  <div class="me-3 text-danger" @click.stop="delete_undone(item)" v-if="quick_delete == true"><i class="fas fa-times"></i></div>
                  <div>{{ item.content }}</div>
                </div>
                <div class="w-50">{{ item.creation_time }}</div>
              </li>
            </ul>
            <ul class="list-group" v-if="label == 'completed'">
              <li class="list-group-item d-flex justify-content-between" v-for="item in completed_list" :key="item">
                <div class="w-75 d-flex justify-content-start">
                  <div class="me-3" @click="item.completed = false"><i class="fas fa-undo-alt"></i></div>
                  <div>{{ item.content }}</div>
                </div>
                <div class="w-50">{{ item.completed_time }}</div>
              </li>
            </ul>
            <ul class="list-group" v-if="label == 'deleted'">
              <li class="list-group-item d-flex justify-content-between" v-for="item in deleted_list" :key="item">
                <div class="w-75 d-flex justify-content-start">
                  <div class="hover_div me-3" @click=" item.isdeleted = false"><i class="fas fa-undo-alt"></i></div>
                  <div>{{ item.content }}</div>
                </div>
                <div class="w-50">{{ item.creation_time }}</div>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'MainBlock',
  // props: {
  //   msg: String
  // }
  data () {
    return {
      start: false,
      pause: false,
      theme: [{
        name: 'adventureTime',
        class: 'theme_1',
        dark_color: 'bg_color_1',
        light_color: 'bg_color_2'
      }, {
        name: 'Jack',
        class: 'theme_2',
        dark_color: 'bg_color_3',
        light_color: 'success'
      }, {
        name: 'forest',
        class: 'theme_3',
        dark_color: 'bg_color_1',
        light_color: 'bg_color_2'
      }],
      now_theme: {
        name: 'adventureTime',
        class: 'theme_1',
        dark_color: 'bg_color_1',
        light_color: 'bg_color_2'
      },
      notice_sound: [{
        name: 'MixSound',
        src: require('../assets/effectSound/MixSound.mp3')
      }, {
        name: 'ElectronGamePop',
        src: require('../assets/effectSound/ElectronGamePop.mp3')
      }, {
        name: 'DogSnarling',
        src: require('../assets/effectSound/DogSnarling.mp3')
      }],
      now_notice: {
        name: 'MixSound',
        src: require('../assets/effectSound/MixSound.mp3')
      },
      notice_sound_record: {
        name: 'MixSound',
        src: require('../assets/effectSound/MixSound.mp3')
      },
      music: [{
        name: 'LazyWalk',
        src: require('../assets/music/LazyWalk.mp3') // require 避免數據轉換成字串??? https://www.jianshu.com/p/52af50e450eb
      }, {
        name: 'Clean and Dance',
        src: require('../assets/music/Clean_and_Dance.mp3')
      }],
      now_music: {
        name: 'LazyWalk',
        src: require('../assets/music/LazyWalk.mp3')
      },
      input_todo: '',
      doingName: '',
      timing: '',
      setting_time: [[25, 0], [5, 0]], // [[pomodoro:min,sec], [rest:min, sec]]
      now_time: [25, 0],
      work_time: true,
      label: '',
      quick_delete: false,
      all_list: []
    }
  },
  methods: {
    // pomodoro
    timing_start () {
      if (!this.doingName) { return }
      this.start = true
      this.pause = false
      this.$refs.audio.play()
      this.timing = setInterval(() => {
        this.countdown()
      }, 1000)
      console.log('play music: ', this.now_music.name)
    },
    timing_pause () {
      this.start = true
      this.pause = true
      clearInterval(this.timing)
      this.$refs.audio.pause()
      console.log('timing_pause')
    },
    countdown () {
      if (this.now_time[1] !== 0) {
        this.now_time[1]--
      } else {
        if (this.now_time[0] !== 0) {
          this.now_time[1] = 59
          this.now_time[0]--
        } else {
          this.now_time[1] = 0
          this.timing_done()
        }
      }
    },
    timing_done () {
      this.effect_sound()
      this.addCompleted()
      this.start = false
      clearInterval(this.timing)
      this.$refs.audio.pause()
      this.$refs.audio.currentTime = 0
      this.work_time = !this.work_time
      console.log('timing_done')
    },
    timing_restart () {
      this.start = false
      this.pause = false
      clearInterval(this.timing)
      this.$refs.audio.pause()
      this.$refs.audio.currentTime = 0
      //
      if (this.work_time) {
        this.now_time = JSON.parse(JSON.stringify(this.setting_time[0]))
      } else {
        this.now_time = JSON.parse(JSON.stringify(this.setting_time[1]))
      }
      console.log('timing_restart')
    },
    change_time () {
      if (this.work_time) {
        this.now_time = JSON.parse(JSON.stringify(this.setting_time[0]))
      } else {
        this.now_time = JSON.parse(JSON.stringify(this.setting_time[1]))
      }
      console.log('change_time')
    },
    effect_sound () {
      if (this.notice_sound) {
        this.$refs.notice.play()
        setTimeout(() => {
          this.$refs.notice.pause()
          console.log('time is over')
        }, 3000)
      }
    },
    // todilist
    add_todo () {
      const content = this.input_todo.trim()
      if (!content) { return }
      const now = new Date()
      this.all_list.push({
        content: content,
        completed: false,
        creation_time: `${now.getFullYear()}.${now.getMonth() + 1}.${now.getDate()} --- ${now.getHours()}:${now.getMinutes()}`,
        completed_time: '',
        isdeleted: false
      })
      if (!this.start) {
        this.doingName = content
      }
      this.input_todo = ''
    },
    change_todo (item) {
      if (item.content !== this.doingName) {
        if (!this.start) {
          this.doingName = item.content
        } else {
          console.log('you are doing something')
          if (confirm('您正在執行其他任務，確定更換任務內容？')) {
            this.doingName = item.content
            this.timing_restart()
          }
        }
      }
    },
    addCompleted () {
      const now = new Date()
      this.todo_list.forEach(item => {
        if (item.content === this.doingName) {
          item.completed = true
          item.completed_time = `${now.getFullYear()}.${now.getMonth() + 1}.${now.getDate()} --- ${now.getHours()}:${now.getMinutes()}`
        }
      })
    },
    quick_delete_mode () {
      this.quick_delete = !this.quick_delete
    },
    delete_undone (the) {
      if (the.content === this.doingName) {
        this.doingName = ''
      }
      the.isdeleted = true
      // this.todo_list.splice(this.todo_list.indexOf(the), 1)
    },
    delete_all_completed () {
      if (confirm('確定要清除所有紀錄？')) {
        this.completed_list.forEach(item => {
          item.completed = false
        })
      }
    },
    delete_all_deleted () {
      if (confirm('確定要清除所有紀錄？')) {
        this.completed_list.forEach(item => {
          item.isdeleted = false
        })
      }
    }
  },
  computed: {
    todo_list () {
      return this.all_list.filter(item => {
        return item.completed === false && item.isdeleted === false
      })
    },
    completed_list () {
      return this.all_list.filter(item => {
        return item.completed === true
      })
    },
    deleted_list () {
      return this.all_list.filter(item => {
        return item.isdeleted === true
      })
    },
    theme_background () {
      return `${this.now_theme.class}`
    },
    theme_bg_color1 () {
      return `bg-${this.now_theme.dark_color} text-light`
    },
    theme_bg_color2 () {
      return `bg-${this.now_theme.light_color} text-light`
    },
    theme_btn_color1 () {
      return `btn-${this.now_theme.dark_color}`
    },
    theme_btn_color2 () {
      return `btn-${this.now_theme.light_color}`
    }
  }
}
</script>

<style scoped lang="scss">
#MainBlock{
  height: 100vh;
  background-size: cover;
  background-position: center;
}
.theme_2{
  background: url(../assets/jack_head.png);
}
.theme_1{
  background: url(../assets/advantureTime.jpg);
}
.theme_3{
  background: url(../assets/forest.jpg);
}
.timing{
  font-size: 6em;
}
.function>button{
  width: 50px;
  height: 50px;
}
// 消除input的上下箭頭
input[type=number]::-webkit-outer-spin-button,
input[type=number]::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}
.hover_div:hover{
  color: rgb(63, 100, 179);
}
</style>
