<template>
  <div id="content" >
    <div class="row" v-cloak >
      <div class="col-sm-12">
        <div class="ibox float-e-margins" style="margin-bottom: 0px">
          <div class="ibox-title">
            资产管理
          </div>
          <div class="ibox-content" style="padding-bottom: 10px">
            <el-row :gutter="15">
              <el-col :span="8">
                <el-row :gutter="10">
                  <el-col :span="12">
                    <el-date-picker
                      size="small"
                      v-model="startDate"
                      :type="datePicker.type"
                      :format="datePicker.format"
                      :clearable="false"
                      :picker-options="pickerOptions"
                      >
                    </el-date-picker>
                  </el-col>
                  <el-col :span="12">
                    <el-date-picker
                      size="small"
                      v-model="endDate"
                      :type="datePicker.type"
                      :format="datePicker.format"
                      :clearable="false"
                      :picker-options="pickerOptions"
                      >
                    </el-date-picker>
                  </el-col>
                </el-row>
              </el-col>
              <el-col :span="16">
                <el-col >
                  <el-select :disabled="branchAble" size="small" clearable v-model="branch" placeholder="选择城市">
                    <el-option label="成都" value="0"></el-option>
                    <el-option label="昆明" value="1"></el-option>
                  </el-select>
                </el-col>
              </el-col>
            </el-row>
          </div>
          <div class="ibox-footer">
            <el-row :gutter="20" style="margin-bottom: 0px;">
              <el-col :span="3" >
              <el-button  icon="search" size="small" @click="query">查询</el-button>
              </el-col>
            </el-row>
          </div>
        </div>
      </div>
    </div>

    <div class="row" v-cloak>
      <div class="col-sm-12">
        <div class="ibox float-e-margins">
          <div class="ibox-title">
            <el-tabs v-model="coModelType" @tab-click="handleClick">
              <el-tab-pane label="租购周供" name="40"></el-tab-pane>
              <el-tab-pane label="租购月供" name="30"></el-tab-pane>
              <el-tab-pane label="租赁" name="20"></el-tab-pane>
            </el-tabs>
          </div>
          <div class="ibox-content" element-loading-text="加载中" style="padding-bottom: 50px">
            <el-table
              v-loading.body="isLoading"
              :data="tableData"
              border
              style="width: 100%">
              <el-table-column
                label="日期"
                width="170"
                fixed="left">
                <template scope="scope">
                  <el-popover trigger="hover" placement="left">
                    <p>{{ scope.row.startDate }} 至 {{scope.row.endDate}}</p>
                    <div slot="reference" class="name-wrapper">
                      <el-icon name="time"></el-icon>
                      <a @click="route(scope.row.startDate)" v-cloak><span style="margin-left: 10px">{{ scope.row.date }}</span></a>
                    </div>
                  </el-popover>
                </template>
              </el-table-column>
              <el-table-column
                label="应收金额"
                width="110">
                <template scope="scope">
                  <el-tag type="gray">{{Number(scope.row.amountReceivable).toLocaleString()}}</el-tag>
                </template>
              </el-table-column>
              <el-table-column
                label="已收金额"
                >
                <el-table-column
                label="微信">
                  <template scope="scope">
                    <el-tag v-if="scope.row.wechatReceived>0" type="success" color="#FFFFFF">{{Number(scope.row.wechatReceived).toLocaleString()}}</el-tag>
                    <div v-else>-</div>
                  </template>
                </el-table-column>
                <el-table-column
                  label="支付宝"
                  width="100">
                  <template scope="scope">
                    <el-tag v-if="scope.row.alipayReceived>0" type="primary" color="#FFFFFF">{{Number(scope.row.alipayReceived).toLocaleString()}}</el-tag>
                    <div v-else>-</div>
                  </template>
                </el-table-column>
                <el-table-column
                  label="建设银行"
                  width="100">
                  <template scope="scope">
                    <el-tag v-if="scope.row.ccbReceived>0" type="gray" :hit="true" color="#FFFFFF">{{Number(scope.row.ccbReceived).toLocaleString()}}</el-tag>
                    <div v-else>-</div>
                  </template>
                </el-table-column>
                <el-table-column
                  label="平安银行"
                  width="100">
                  <template scope="scope">
                    <el-tag style="color: #fd6720; border-color: #fd6720;" v-if="scope.row.pinganReceived>0"  color="#FFFFFF">{{Number(scope.row.pinganReceived).toLocaleString()}}</el-tag>
                    <div v-else>-</div>
                  </template>
                </el-table-column>
                <el-table-column
                  label="招商银行"
                  width="100">
                  <template scope="scope">
                    <el-tag style="color: #fd6720; border-color: #fd6720;" v-if="scope.row.cmbReceived>0"  color="#FFFFFF">{{Number(scope.row.cmbReceived).toLocaleString()}}</el-tag>
                    <div v-else>-</div>
                  </template>
                </el-table-column>
                <el-table-column
                  label="POS">
                  <template scope="scope">
                    <el-tag style="color: #fd6720; border-color: #fd6720;" v-if="scope.row.posReceived>0"  color="#FFFFFF">{{Number(scope.row.posReceived).toLocaleString()}}</el-tag>
                    <div v-else>-</div>
                  </template>
                </el-table-column>

              </el-table-column>
              <el-table-column
                label="已收总额" width="110">
                <template scope="scope">
                  <el-tag type="danger" :close-transition="true" :hit="true" color="#FFFFFF" v-if="scope.row.amountReceived>0">{{Number(scope.row.amountReceived).toLocaleString()}}</el-tag>
                </template>
              </el-table-column>
              <el-table-column
                prop="driverNoReceivable"
                label="应收人数" width="100">
              </el-table-column>
              <el-table-column
                prop="driverNoReceived"
                label="已收人数"
                width="100">
              </el-table-column>
              <el-table-column
                label="操作"
                width="100"
              fixed="right">
                <template scope="scope">
                  <el-button size="mini">查看收款</el-button>
                </template>
              </el-table-column>
            </el-table>
            <el-pagination
              @size-change="handleSizeChange"
              @current-change="handleCurrentChange"
              :current-page="pageNum"
              :page-sizes="[10, 50, 100]"
              :page-size="pageSize"
              layout="total, sizes, prev, pager, next"
              :total="total"
              class="pull-right" style="margin-top: 8px">
            </el-pagination>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import axios from 'axios'

  export default {
    data () {
      return {
        isLoading: false,
        pickerOptions: {
          firstDayOfWeek: 1,
          disabledDate (time) {
            return time >= new Date()
          }
        },
        tableData: [],
        startDate: '',
        endDate: '',
        coModelType: '40',
        total: 5,
        pageNum: 1,
        pageSize: 10,
        branch: '',
        branchAble: true,
        datePicker: {
          type: 'week',
          format: 'yyyy-MM-dd 第 WW 周'
        },
        startMonthVal: '',
        endMonthVal: ''
      }
    },
    methods: {
      handleClick (tab) {
        if (tab.name === '40') {
          this.dateInitial()
          this.datePicker.type = 'week'
          this.datePicker.format = 'yyyy-MM-dd 第 WW 周'
        } else if (tab.name === '30') {
          this.datePicker.type = 'month'
          this.datePicker.format = 'yyyy 年 MM 月'
        }
        this.coModelType = tab.name
        this.fetchData()
      },
      query () {
        this.pageNum = 1
        this.pageSize = 10
        this.fetchData()
      },
      fetchData () {
        const _this = this
        this.isLoading = true
        axios.get('/api/manage/period_payment/general_list.do',
          {
            params: {
              branch: this.branch,
              startDate: this.startDate.getTime(),
              endDate: this.endDate.getTime(),
              coModelType: this.coModelType,
              pageSize: this.pageSize,
              pageNum: this.pageNum
            }
          }).then(function (res) {
            if (res.data.status === 0) {
              _this.total = res.data.data.total
              _this.tableData = res.data.data.list
              _this.isLoading = false
            } else if (res.data.status === 10) {
              _this.$router.push({name: 'login'})
            }
          })
      },
      handleSizeChange (val) {
        this.pageSize = val
        this.fetchData()
      },
      handleCurrentChange (val) {
        this.pageNum = val
        this.fetchData()
      },
      route (data) {
        // 不能匹配空参数,所以将branch码直接+1,然后再相应匹配
        this.$router.push({name: 'period-payment-list', params: {co_model_type: this.coModelType, date: data, branch: this.branch + 1}})
      },
      dateInitial () {
        if (sessionStorage.getItem('startDate') === null || sessionStorage.getItem('endDate') === null) {
          this.endDate = new Date()
          this.startDate = new Date()
          this.startDate.setTime(this.startDate.getTime() - 3600 * 1000 * 24 * 7 * 5)
        } else {
          this.endDate = new Date(parseInt(sessionStorage.getItem('endDate')))
          this.startDate = new Date(parseInt(sessionStorage.getItem('startDate')))
        }
      }
    },
    watch: {
      startDate (val) {
        sessionStorage.setItem('startDate', val.getTime())
      },
      endDate (val) {
        sessionStorage.setItem('endDate', val.getTime())
      }
    },
    created () {
      if (this.userBranch === 0) {
        this.branch = '0'
      } else if (this.userBranch === 1) {
        this.branch = '1'
      } else if (this.userBranch === -1) {
        this.branchAble = false
      }
      this.dateInitial()
      this.fetchData()
    }
  }
</script>

<style>
  .el-tabs__header {
    margin: 0 0 0 0px;
  }

</style>
