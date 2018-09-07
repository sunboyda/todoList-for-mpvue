<template>
  <div class="container">
  
      <div class="header">
        <div class="text">+</div>
          <div class="input">
            <input type="text" placeholder="Anything href..." @confirm="add" v-model="content" autofocus="autofocus"> 
          </div>
      </div>
      <div v-show="list.length>0">
      <div class="list">
        <div class="items" v-for="v in list" :key="v.id">
          <div @tap="checkSelect(v.id)">
            <icon type="success" size="20" class="left" v-show="v.isSelect"></icon>
            <icon type="circle" size="20"  class="left" v-show="!v.isSelect"></icon>
          </div>
          <div class="content">{{v.name}}</div> 
          <icon type="clear" size="20" @tap='del(v.id)'></icon>
        </div>
      </div>
      <div class="footer">
        <div @tap="checkAll">Toggle all</div>
        <div>{{sum}} items left</div>
        <div @tap="clear" v-show="sum < list.length">clear completed</div>
      </div>
    </div>
    <div class="empty"  v-show="list.length===0">
      <div class="top">Congratulations</div>
      <div class="bottom">There's no more work left</div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      list: [
        { id: 1, name: '第一条', isSelect: false },
        { id: 2, name: '第二条', isSelect: true }
      ],
      content: ''
    }
  },
  methods: {
    add(e) {
      let value = e.target.value
      let list = this.list
      let id = 0
      if (list.length === 0) {
        id = 1
      } else {
        id = list[list.length - 1].id + 1
      }
      this.list.push({ id: id, name: value, isSelect: false })
      this.content = ''
      this.local()
    },
    checkSelect(id) {
      let item = this.list.find(item => item.id === id)
      item.isSelect = !item.isSelect
    },
    del(id) {
      let item = this.list.find(item => item.id === id)
      this.list = this.list.filter(el => el !== item)
      this.local()
    },
    checkAll() {
      let all = this.list.every(item => item.isSelect)
      if (all) {
        this.list.forEach(el => {
          el.isSelect = false
        })
      } else {
        this.list.forEach(el => {
          el.isSelect = true
        })
      }
      this.local()
    },
    clear() {
      this.list = this.list.filter(item => item.isSelect === false)
      this.local()
    },
    local() {
      wx.setStorageSync('todo', this.list)
    }
  },
  computed: {
    sum() {
      let num = 0
      this.list.forEach(item => {
        if (item.isSelect === false) {
          num++
        }
      })
      return num
    }
  },
  onShow() {
    this.list = wx.getStorageSync('todo') || []
  }
}
</script>   

<style scoped>
* {
  margin: 0;
  padding: 0;
  list-style: none;
  box-sizing: border-box;
}
.container {
  padding: 20px;
}
.header {
  display: flex;
  border: 1px solid #ccc;
  align-items: center;
  padding-left: 5px;
  border-radius: 8px;
}
.header .text {
  font-size: 35px;
  font-weight: 600;
  line-height: 35px;
  color: #ccc;
  margin-right: 5px;
}
.header .input {
  font-size: 14px;
  flex: 1;
}
.header .input input {
  width: 100%;
}
.list {
  margin-top: 15px;
}
.items {
  display: flex;
  align-items: center;
  height: 25px;
  padding: 5px;
  font-size: 14px;
  border: 1px solid #ccc;
  border-radius: 8px;
  margin-top: 5px;
  color: #333;
}
.items .left {
  margin-right: 5px;
}
.items .content {
  flex: 1;
}
.footer {
  margin-top: 10px;
  display: flex;
  font-size: 14px;
  color: #999;
  justify-content: space-between;
}
.empty {
  text-align: center;
  margin-top: 40px;
}
.empty .top {
  font-size: 24px;
  margin-bottom: 15px;
  color: #777;
}
.empty .bottom {
  font-size: 14px;
  color: #999;
}
</style>
