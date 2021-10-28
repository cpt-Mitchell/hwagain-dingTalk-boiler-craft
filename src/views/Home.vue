<template>
  <div style="height: 100vh; padding: 5px 10px; width: 100vw; overflow: hidden">
    <div class="title">
      <div class="titleText">
        <span>检验人：{{ userName }}</span>
      </div>
      <div class="titleText">
        <span>取样时间：{{ checkTime }}</span>
      </div>
    </div>
    <table class="todo-table" style="width: calc(100vw - 20px)">
      <thead style="background-color: #92cae1; z-index: 1">
        <td class="thH" colspan="2" style="width: 40vw">蒸发站</td>
        <td class="thH" style="width: 17.5vw">温度</td>
        <td class="thH" style="width: 17.5vw">波美度</td>
        <td class="thH" style="width: 20vw">备注</td>
      </thead>
    </table>
    <div class="tbdiv">
      <table class="todo-table" style="width: calc(100vw - 20px)">
        <tbody v-if="todoList.length">
          <template v-for="(item, index) in todoList">
            <tr :key="index">
              <td :colspan="item.vaporizeStationZh === '结晶效' ? 2 : 1" style="width: 25vw">
                {{ item.vaporizeStationZh || '-' }}
              </td>
              <td v-if="item.vaporizeStationZh !== '结晶效'" style="width: 15vw">
                {{ item.effectZh || '-' }}
              </td>
              <td class="thB" style="width: 17.5vw">
                <input class="tb-input" type="text" placeholder="请录入" v-model="item.oc" />
              </td>
              <td class="thB" style="width: 17.5vw">
                <input class="tb-input" type="text" placeholder="请录入" v-model="item.baume" />
              </td>
              <td class="thB" style="width: 20vw" @click="stateSelector(item)">
                <input
                  class="tb-input"
                  readonly
                  type="text"
                  :style="{ color: !item.remark ? '#b6abbe' : '#333' }"
                  placeholder="请选择"
                  v-model="item.remark"
                />
              </td>
            </tr>
          </template>
        </tbody>
        <tr v-else>
          <td colspan="4" style="text-align: center">{{ isLoading === 0 ? '正在查询' : '暂无数据' }}</td>
        </tr>
      </table>
    </div>
    <div class="foot-bar">
      <div style="display: flex; justify-content: center; item-align: center; padding: 5px 0; width: 100vw">
        <button class="search-btn" @click="confirm">提交</button>
      </div>
    </div>
  </div>
</template>

<script>
import { mapModules } from 'vuet'
import { API2 } from '@/api'
import request from '@/utils/httpUtil'
export default {
  mixins: [mapModules({ home: 'home' })],
  data() {
    return {
      isLoading: 0,
      checkTime: '-',
      allData: {},
      userName: '-',
      todoList: [],
      remarkOptions: ['请选择', '冲罐', '堵塞']
    }
  },
  mounted() {
    this.getUserInfo()
    dd.ui.webViewBounce.disable()
    dd.biz.navigation.setTitle({
      title: '波美度检测结果录入', // 控制标题文本，空字符串表示显示默认文本
      onSuccess: function (result) {},
      onFail: function (err) {}
    })
  },
  methods: {
    getUserInfo() {
      this.userName = this.home._LOGINUSER_.name || ''
      this.init()
    },
    stateSelector(item) {
      dd.device.notification.actionSheet({
        title: '请选择是否冲罐', // 标题
        cancelButton: '取消', // 取消按钮文本
        otherButtons: this.remarkOptions,
        onSuccess: result => {
          if (result.buttonIndex !== -1 && result.buttonIndex !== 0) {
            item.remark = this.remarkOptions[result.buttonIndex]
          } else if (result.buttonIndex === 0) {
            item.remark = ''
          }
          // onSuccess将在点击button之后回调
          /* {
            buttonIndex: 0 //被点击按钮的索引值，Number，从0开始, 取消按钮为-1
        } */
        },
        onFail: function (err) {
          dAlert(JSON.stringify(err))
        }
      })
    },
    init() {
      let params = {
        dingName: this.userName
      }
      this.isLoading = 0
      request.get(API2.GET_INFO, { params }).then(res => {
        this.isLoading = 1
        const data = res.data || {}
        if (data.success) {
          this.checkTime = data.data.sampleTime || ''
          this.todoList = data.data.denseInputDetailDtos || []
          this.allData = data.data || {}
        } else {
          dAlert(data.msg || '查询初始化信息失败')
        }
      })
    },
    confirm() {
      this.allData.denseInputDetailDtos = this.todoList
      this.allData.checkUser = this.userName || ''
      request.post(API2.POST_INFO, this.allData).then(res => {
        if (res.success) {
          dAlert(res.msg || '提交成功')
        } else {
          dAlert(res.msg || '提交失败')
        }
      })
    }
  }
}
</script>

<style lang="less" scoped>
* {
  font-size: 26px;
}
.tb-input {
  width: 100%;
  padding: 10px 10px;
}
.tbdiv {
  // padding-bottom: 45px;
  height: calc(100% - 80px - 68px - 90px);
  overflow-x: hidden;
  overflow-y: auto;
}
.foot-bar {
  // position: fixed;
  bottom: 0;
  height: 90px;
  border-top: 1px solid #ccc;
  z-index: 1;
  background-color: #fff;
}
.title {
  height: 80px;
  background-color: #fff;
  display: flex;
  flex-direction: row;
  z-index: 1;
}
.titleText {
  height: 80px;
  display: flex;
  align-items: center;
  padding: 10px;
  flex-grow: 1;
  // span {
  // font-size: 20px;
  //   // display: block;
  //   // width:100%;
  // }
}
// .left {
//   text-align: left;
// }
// .right {
//   text-align: right;
// }
.thB {
  padding: 5px;
  // font-size: 20px;
}
.thH {
  padding: 10px;
  // font-size: 20px;
}
span {
  color: #333;
}
input {
  text-align: center;
  border: 1px solid #a5a5a5;
}
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

.head {
  // font-size: 14px;
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
  border-collapse: collapse;
  border-spacing: 0;
  th,
  td {
    text-align: center;
    // font-size: 20px;
    height: 68px;
  }
  td:not(:last-child),
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
