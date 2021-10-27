<template>
  <div>
    <div style="position: fixed; width: 100vw; z-index: 1; font-size: 16px">
      <div style="height: 40px; display: flex; align-items: center;padding: 10px">
        <span>检验人：{{ userName }}</span>
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
                {{ item.vaporizeStationZh || '-' }}
              </td>
              <td v-if="item.nn !== '结晶效'" style="width: 20vw; padding: 15px 5px;font-size: 14px">
                {{ item.effectZh || '-' }}
              </td>
              <td style="width: 20vw; padding: 15px 5px;font-size: 14px">
                <input type="text" placeholder="请录入" width="20vw" v-model="item.oc" />
              </td>
              <td style="width: 20vw; padding: 15px 5px;font-size: 14px">
                <input type="text" placeholder="请录入" v-model="item.baume" />
              </td>
              <td style="width: 20vw; padding: 15px 5px;font-size: 14px" @click="stateSelector(index)">
                <input type="text" placeholder="请选择" v-model="item.remark" />
              </td>
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
import { mapModules } from 'vuet'
import { API2 } from '@/api'
import request from '@/utils/httpUtil'
export default {
  mixins: [mapModules({ home: 'home' })],
  data() {
    return {
      checkTime: '2021-10-27',
      allData: {},
      userName: '',
      todoList: [
        { nn: '结晶效', zn: 1, cn: 2, in: 3, dn: 4 },
        { nn: '1#蒸发站', zn: 1, cn: 2, in: 3, dn: 4 }
      ],
      remarkOptions: ['冲罐', '不冲罐']
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
    getUserInfo(index) {
      this.userName = this.home._LOGINUSER_.name || ''
      this.init()
    },
    stateSelector() {
      dd.device.notification.actionSheet({
        title: '请选择是否冲罐', // 标题
        cancelButton: '取消', // 取消按钮文本
        otherButtons: this.remarkOptions,
        onSuccess: result => {
          if (result.buttonIndex === 0) {
            this.todoList[index] = this.remarkOptions[result.buttonIndex]
          } else if (result.buttonIndex === 1) {
            this.todoList[index].remark = ''
          }
          // onSuccess将在点击button之后回调
          /* {
            buttonIndex: 0 //被点击按钮的索引值，Number，从0开始, 取消按钮为-1
        } */
        },
        onFail: function(err) {
          dAlert(JSON.stringify(err))
        }
      })
    },
    init() {
      let params = {
        dingName: this.userName
      }
      request.get(process.env.VUE_APP_BASE_API2 + API2.GET_INFO, { params }).then(res => {
        if (res.success) {
          this.checkTime = res.data.sampleTime || ''
          this.todoList = res.data.denseInputDetailDtos || []
          this.allData = res.data || {}
        } else {
          dAlert(res.msg || '查询初始化信息失败')
        }
      })
    },
    confirm() {
      this.allData.denseInputDetailDtos = this.todoList
      request.post(process.env.VUE_APP_BASE_API2 + API2.GET_INFO, this.allData).then(res => {
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
