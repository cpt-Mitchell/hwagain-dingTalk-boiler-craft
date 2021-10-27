<template>
  <div>
    <div style="position: fixed; width: 100vw; z-index: 1; font-size: 16px">
      <div style="height: 40px; display: flex; align-items: center;padding: 10px">
        <span>检验人：{{ checker }}</span>
      </div>
      <div style="height: 40px; display: flex; align-items: center;padding: 10px">
        <span>取样时间：{{ checkTime }}</span>
      </div>
    </div>
    <div class="cantain" style="padding-top: 80px">
      <table class="todo-table">
        <thead style="position: fixed; width: 100vw; background-color: rgb(52, 196, 253); z-index: 0">
          <th colspan="2" style="width: 40vw; padding: 10px; font-size: 16px">蒸发站</th>
          <th style="width: 20vw; padding: 10px; font-size: 16px">温度</th>
          <th style="width: 20vw; padding: 10px; font-size: 16px">波美度</th>
          <th style="width: 20vw; padding: 10px; font-size: 16px">备注</th>
        </thead>
      </table>
    </div>
    <div class="cantain" style="padding-top: 38px; padding-bottom: 45px">
      <table class="todo-table">
        <tbody>
          <template v-for="(item, index) in todoList">
            <tr :key="index">
              <td :colspan="item.nn === '结晶效' ? 2 : 1" style="width: 20vw; padding: 15px 5px;font-size: 14px">
                {{ item.nn || '-' }}
              </td>
              <td v-if="item.nn !== '结晶效'" style="width: 20vw; padding: 15px 5px;font-size: 14px">
                {{ item.zn || '-' }}
              </td>
              <td style="width: 20vw; padding: 15px 5px;font-size: 14px">{{ item.cn || '-' }}</td>
              <td style="width: 20vw; padding: 15px 5px;font-size: 14px">{{ item.in || '-' }}</td>
              <td style="width: 20vw; padding: 15px 5px;font-size: 14px">{{ item.dn || '-' }}</td>
            </tr>
          </template>
        </tbody>
      </table>
    </div>
    <div style="position: fixed; bottom: 0; height: 45px; border-top: 1px solid #ccc; z-index: 1">
      <div style="display: flex; justify-content: center; item-align: center; padding: 5px 0; width: 100vw">
        <button class="search-btn" @click="confirm">提交</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      checker: '曾令霜',
      checkTime: '2021-10-27',
      userName: '',
      todoList: [
        { nn: '结晶效', zn: 1, cn: 2, in: 3, dn: 4 },
        { nn: '1#蒸发站', zn: 1, cn: 2, in: 3, dn: 4 }
      ]
    }
  },
  mounted() {
    dd.biz.navigation.setTitle({
      title: '蒸发黑液浓度人工检测结果录入', // 控制标题文本，空字符串表示显示默认文本
      onSuccess: function(result) {},
      onFail: function(err) {}
    })
    this.getUserInfo()
  },
  methods: {
    getUserInfo() {
      this.userName = window.localStorage.getItem('ding_user_id') || 'A011814'
      if (this.userId) {
        this.userCode = Encrypt(this.userId)
        this.doSearch()
      }
    },
    confirm() {}
  }
}
</script>

<style lang="less" scoped>
/* .cantain {
  padding-top: 72px;
} */
.search-btn {
  padding: 16px 60px;
  border: none;
  background: #0099ff;
  color: #fff;
  margin-left: 25px;
  &:active {
    opacity: 0.8;
  }
}
.van-cell {
  background-color: #f0f2f5;
  padding: 10px 15px;
  height: 34px;
  line-height: 18px;
}
.tips {
  font-size: 14px;
  min-height: 34px;
  border-spacing: 0;
  text-align: center;
  border: 1px solid rgb(52, 196, 253);
  border-left: none;
  border-right: none;
  border-top: none;
}
.head {
  font-size: 14px;
  min-height: 36px;
  border-spacing: 0;
  font-weight: bold;
  padding: 7px 0;
  text-align: center;
  border: 1px solid #ccc;
  border-left: none;
  border-top: none;
}
.van-field {
  background-color: #f0f2f5;
}
.todo-table {
  width: 100vw;
  border-collapse: collapse;
  border-spacing: 0;
  th,
  td {
    text-align: center;
    font-size: 14px;
    height: 36px;
  }
  td,
  th {
    border: 1px solid #ccc;
  }
  td {
    &:first-child {
      border-left: none;
    }
  }
  td,
  th {
    &:first-child {
      border-left: none;
    }
    &:last-child {
      border-right: none;
    }
  }
}
</style>
