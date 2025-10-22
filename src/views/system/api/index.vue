<template>
  <div>
    <el-card class="container-card" shadow="always">
      <el-form size="mini" :inline="true" :model="params" class="demo-form-inline">
        <!--        <el-form-item label="访问路径">-->
        <!--          <el-input v-model.trim="params.path" clearable placeholder="访问路径" @clear="search" />-->
        <!--        </el-form-item>-->
        <!--        <el-form-item label="所属类别">-->
        <!--          <el-input v-model.trim="params.category" clearable placeholder="所属类别" @clear="search" />-->
        <!--        </el-form-item>-->
        <!--        <el-form-item label="请求方法">-->
        <!--          <el-select v-model.trim="params.method" clearable placeholder="请求方式" @change="search" @clear="search">-->
        <!--            <el-option label="GET[获取资源]" value="GET" />-->
        <!--            <el-option label="POST[新增资源]" value="POST" />-->
        <!--            <el-option label="PUT[全部更新]" value="PUT" />-->
        <!--            <el-option label="PATCH[增量更新]" value="PATCH" />-->
        <!--            <el-option label="DELETE[删除资源]" value="DELETE" />-->
        <!--          </el-select>-->
        <!--        </el-form-item>-->
        <!--        <el-form-item label="创建人">-->
        <!--          <el-input v-model.trim="params.creator" clearable placeholder="创建人" @clear="search" />-->
        <!--        </el-form-item>-->
        <!--        <el-form-item>-->
        <!--          <el-button :loading="loading" icon="el-icon-search" type="primary" @click="search">查询</el-button>-->
        <!--        </el-form-item>-->
        <el-form-item class="time-range-container">
          <el-date-picker
            v-model="dateRange"
            type="daterange"
            range-separator="-"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
            :picker-options="pickerOptions"
            value-format="timestamp"
            format="yyyy-MM-dd"
            @change="handleRangeChange"
          />
        </el-form-item>
        <el-form-item>
          <el-button :loading="loading" icon="el-icon-plus" type="warning" @click="create">新增</el-button>
        </el-form-item>
        <el-form-item>
          <el-button :disabled="multipleSelection.length === 0" :loading="loading" type="danger" @click="startTask">任务开始</el-button>
        </el-form-item>
      </el-form>

      <el-table v-loading="loading" :data="tableData" border stripe style="width: 100%" @selection-change="handleSelectionChange">
        <el-table-column type="selection" width="55" align="center" />
        <el-table-column show-overflow-tooltip sortable prop="GameNo" label="游戏编号" />
        <el-table-column show-overflow-tooltip sortable prop="GameName" label="游戏名称" />
        <el-table-column show-overflow-tooltip sortable prop="MerchantName" label="商户名称" /> <!--align="center">-->
        <!--          <template slot-scope="scope">-->
        <!--            <el-tag size="small" :type="scope.row.method | methodTagFilter" disable-transitions>{{ scope.row.method }}</el-tag>-->
        <!--          </template>-->
        <!--        </el-table-column>-->
        <el-table-column show-overflow-tooltip sortable prop="MerchantNo" label="商户编号" />
        <el-table-column show-overflow-tooltip sortable prop="ProductId" label="商品编码" />
        <el-table-column show-overflow-tooltip sortable prop="ProductName" label="商品名称" />
        <el-table-column show-overflow-tooltip sortable prop="TradeType" label="交易类型" />
        <el-table-column show-overflow-tooltip sortable prop="TradePriceCent" label="支付价格" />
        <el-table-column show-overflow-tooltip sortable prop="Currency" label="支付币种" />
        <el-table-column show-overflow-tooltip sortable prop="ServiceFeeCent" label="服务费" />
        <el-table-column show-overflow-tooltip sortable prop="PayType" label="支付方式" />
        <el-table-column show-overflow-tooltip sortable prop="Remarks" label="备注" />
        <el-table-column show-overflow-tooltip sortable prop="Count" label="订单数量" />
        <!--        <el-table-column fixed="right" label="操作" align="center" width="120">-->
        <!--          <template slot-scope="scope">-->
        <!--            <el-tooltip content="任务开始" effect="dark" placement="top">-->
        <!--              <el-button size="mini" icon="el-icon-edit" circle type="primary" @click="update(scope.row)" />-->
        <!--            </el-tooltip>-->
        <!--            <el-tooltip class="delete-popover" content="删除" effect="dark" placement="top">-->
        <!--              <el-popconfirm title="确定删除吗？" @onConfirm="singleDelete(scope.row.ID)">-->
        <!--                <el-button slot="reference" size="mini" icon="el-icon-delete" circle type="danger" />-->
        <!--              </el-popconfirm>-->
        <!--            </el-tooltip>-->
        <!--          </template>-->
        <!--        </el-table-column>-->
      </el-table>

      <el-pagination
        :current-page="params.pageNum"
        :page-size="params.pageSize"
        :total="total"
        :page-sizes="[1, 5, 10, 30]"
        layout="total, prev, pager, next, sizes"
        background
        style="margin-top: 10px;float:right;margin-bottom: 10px;"
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
      />

      <el-dialog :title="dialogFormTitle" :visible.sync="dialogFormVisible">
        <el-form ref="dialogForm" size="small" :model="dialogFormData" :rules="dialogFormRules" label-width="120px">
          <el-form-item label="游戏编号" prop="GameNo">
            <el-input v-model.trim="dialogFormData.GameNo" placeholder="游戏编号" />
          </el-form-item>
          <el-form-item label="游戏名称" prop="GameName">
            <el-input v-model.trim="dialogFormData.GameName" placeholder="游戏名称" />
          </el-form-item>
          <el-form-item label="商户编号" prop="MerchantNo">
            <el-input v-model.trim="dialogFormData.MerchantNo" placeholder="商户编号" />
          </el-form-item>
          <el-form-item label="商户名称" prop="MerchantName">
            <el-input v-model.trim="dialogFormData.MerchantName" placeholder="商户名称" />
          </el-form-item>
          <el-form-item label="商品编码" prop="ProductId">
            <el-input v-model.trim="dialogFormData.ProductId" placeholder="商品编码" />
          </el-form-item>
          <el-form-item label="商品名称" prop="ProductName">
            <el-input v-model.trim="dialogFormData.ProductName" placeholder="商品名称" />
          </el-form-item>
          <el-form-item label="交易类型" prop="TradeType">
            <el-input v-model.trim="dialogFormData.TradeType" placeholder="交易类型" />
          </el-form-item>
          <el-form-item label="支付价格" prop="TradePriceCent">
            <el-input v-model.trim="dialogFormData.TradePriceCent" placeholder="支付价格" />
          </el-form-item>
          <el-form-item label="支付币种" prop="Currency">
            <el-input v-model.trim="dialogFormData.Currency" placeholder="支付币种" />
          </el-form-item>
          <el-form-item label="服务费" prop="ServiceFeeCent">
            <el-input v-model.trim="dialogFormData.ServiceFeeCent" placeholder="服务费" />
          </el-form-item>
          <el-form-item label="支付方式" prop="PayType">
            <el-input v-model.trim="dialogFormData.PayType" placeholder="支付方式" />
          </el-form-item>
          <el-form-item label="订单数量" prop="Count">
            <el-input v-model.trim="dialogFormData.Count" placeholder="订单数量" />
          </el-form-item>
          <!--          <el-form-item label="请求方式" prop="method">-->
          <!--            <el-select v-model.trim="dialogFormData.method" placeholder="请选择请求方式">-->
          <!--              <el-option label="GET[获取资源]" value="GET" />-->
          <!--              <el-option label="POST[新增资源]" value="POST" />-->
          <!--              <el-option label="PUT[全部更新]" value="PUT" />-->
          <!--              <el-option label="PATCH[增量更新]" value="PATCH" />-->
          <!--              <el-option label="DELETE[删除资源]" value="DELETE" />-->
          <!--            </el-select>-->
          <!--          </el-form-item>-->
          <el-form-item label="备注" prop="Remarks">
            <el-input v-model.trim="dialogFormData.Remarks" type="textarea" placeholder="备注" show-word-limit maxlength="100" />
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button size="mini" @click="cancelForm()">取 消</el-button>
          <el-button size="mini" :loading="submitLoading" type="primary" @click="submitForm()">确 定</el-button>
        </div>
      </el-dialog>
      <Toast ref="toast" />
    </el-card>
  </div>
</template>

<script>
import { getApis, updateApiById, batchDeleteApiByIds, createOrder } from '@/api/system/api'
import Toast from '@/components/Toast/Toast.vue'

export default {
  name: 'Api',
  components: { Toast },
  filters: {
    methodTagFilter(val) {
      if (val === 'GET') {
        return ''
      } else if (val === 'POST') {
        return 'success'
      } else if (val === 'PUT') {
        return 'info'
      } else if (val === 'PATCH') {
        return 'warning'
      } else if (val === 'DELETE') {
        return 'danger'
      } else {
        return 'info'
      }
    }
  },
  data() {
    return {
      // 查询参数
      params: {
        path: '',
        method: '',
        category: '',
        creator: '',
        pageNum: 1,
        pageSize: 10
      },
      dateRange: [],
      pickerOptions: {
        disabledDate(time) { return time.getTime() < Date.now() - 8.64e7 }
      },
      // 表格数据
      tableData: [],
      total: 0,
      loading: false,
      startTime: Date.now(),
      endTime: Date.now(),
      // dialog对话框
      submitLoading: false,
      dialogFormTitle: '',
      dialogType: '',
      dialogFormVisible: false,
      dialogFormData: {
        Count: '',
        MerchantNo: '',
        GameNo: '',
        GameName: '',
        TradeType: '',
        ProductId: '',
        ProductName: '',
        MerchantName: '',
        Currency: '',
        TradePriceCent: '',
        ServiceFeeCent: '',
        PayType: '',
        Remarks: ''
      },
      dialogFormRules: {
        EndTime: [
          { required: true, message: '请选择结束时间', trigger: 'blur' }
          // { min: 1, max: 100, message: '长度在 1 到 100 个字符', trigger: 'blur' }
        ],
        Count: [
          { required: true, message: '请输入任务数量', trigger: 'blur' },
          { min: 1, max: 50, message: '最多每天900个任务', trigger: 'blur' }
        ],
        StartTime: [
          { required: true, message: '请选择开始时间', trigger: 'change' }
        ],
        MerchantNo: [
          { required: false, message: '请输入商户编号', trigger: 'blur' },
          { min: 0, max: 100, message: '长度在 0 到 100 个字符', trigger: 'blur' }
        ]
      },

      // 删除按钮弹出框
      popoverVisible: false,
      // 表格多选
      multipleSelection: []
    }
  },
  created() {
    this.getData()
    // this.getTableData()
  },
  methods: {
    // 查询
    search() {
      // this.params.pageNum = 1
      // this.getTableData()
      this.getData()
    },

    // 获取表格数据
    async getTableData() {
      this.loading = true
      try {
        const { data } = await getApis(this.params)
        this.tableData = data.apis
        this.total = data.total
      } finally {
        this.loading = false
      }
    },

    // 从本地获取缓存数据
    getData() {
      const orderData = JSON.parse(localStorage.getItem('orderInfo'))
      if (orderData !== null) {
        console.log(orderData)
        this.tableData = orderData.tableData
      } else {
        this.tableData = []
      }
      console.log(this.tableData)
    },

    handleRangeChange(val) {
      if (val && val.length === 2) {
        const [startTimestamp, endTimestamp] = val
        this.startTime = startTimestamp
        this.endTime = endTimestamp
      }
    },

    // 将数据保存到本地
    saveData(dialogFormData) {
      if (this.tableData === null) this.tableData = []
      this.tableData.push(dialogFormData)
      this.orderData = {
        startTime: this.startTime,
        endTime: this.endTime,
        tableData: this.tableData
      }
      // this.tableData.push(dialogFormData)
      localStorage.setItem('orderInfo', JSON.stringify(this.orderData))
    },

    async startTask() {
      if (this.endTime <= this.startTime) {
        this.$refs.toast.show('请选择时间')
        return
      }
      this.orderData = {
        startTime: this.startTime,
        endTime: this.endTime,
        data: this.tableData
      }
      const { message } = await createOrder(this.orderData)
      if (message) {
        // eslint-disable-next-line no-undef
        // showPopup({
        //   content: '任务创建成功',
        //   type: 'success'
        // })
        this.$refs.toast.show('任务创建成功')
      }
    },

    // 新增
    create() {
      this.dialogFormTitle = '新增任务'
      this.dialogType = 'create'
      this.dialogFormVisible = true
    },

    // 修改
    update(row) {
      this.dialogFormData.ID = row.ID
      this.dialogFormData.path = row.path
      this.dialogFormData.category = row.category
      this.dialogFormData.method = row.method
      this.dialogFormData.desc = row.desc

      this.dialogFormTitle = '修改任务'
      this.dialogType = 'update'
      this.dialogFormVisible = true
    },

    // 提交表单
    submitForm() {
      this.$refs['dialogForm'].validate(async valid => {
        if (valid) {
          let msg = ''
          this.submitLoading = true
          try {
            if (this.dialogType === 'create') {
              // const { message } = await createApi(this.dialogFormData)
              // msg = message
              this.saveData(this.dialogFormData)
            } else {
              const { message } = await updateApiById(this.dialogFormData.ID, this.dialogFormData)
              msg = message
            }
          } finally {
            this.submitLoading = false
          }

          this.resetForm()
          // this.getTableData()
          this.getData()
          this.$message({
            showClose: true,
            message: msg,
            type: 'success'
          })
        } else {
          this.$message({
            showClose: true,
            message: '表单校验失败',
            type: 'error'
          })
          return false
        }
      })
    },

    // 提交表单
    cancelForm() {
      this.resetForm()
    },

    resetForm() {
      this.dialogFormVisible = false
      this.$refs['dialogForm'].resetFields()
      this.dialogFormData = {
        path: '',
        category: '',
        method: '',
        desc: ''
      }
    },

    // 批量删除
    batchDelete() {
      this.$confirm('此操作将永久删除, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(async res => {
        this.loading = true
        const apiIds = []
        this.multipleSelection.forEach(x => {
          apiIds.push(x.ID)
        })
        let msg = ''
        try {
          const { message } = await batchDeleteApiByIds({ apiIds: apiIds })
          msg = message
        } finally {
          this.loading = false
        }

        // this.getTableData()
        this.getData()
        this.$message({
          showClose: true,
          message: msg,
          type: 'success'
        })
      }).catch(() => {
        this.$message({
          showClose: true,
          type: 'info',
          message: '已取消删除'
        })
      })
    },

    // 表格多选
    handleSelectionChange(val) {
      this.multipleSelection = val
    },

    // 单个删除
    async singleDelete(Id) {
      this.loading = true
      let msg = ''
      try {
        const { message } = await batchDeleteApiByIds({ apiIds: [Id] })
        msg = message
      } finally {
        this.loading = false
      }

      // this.getTableData()
      this.getData()
      this.$message({
        showClose: true,
        message: msg,
        type: 'success'
      })
    },

    // 分页
    handleSizeChange(val) {
      this.params.pageSize = val
      // this.getTableData()
      this.getData()
    },
    handleCurrentChange(val) {
      this.params.pageNum = val
      // this.getTableData()
      this.getData()
    }
  }
}
</script>

<style scoped>
  .container-card{
    margin: 10px;
  }

  .delete-popover{
    margin-left: 10px;
  }

  .time-range-container {
    display: flex;
    gap: 10px;
    align-items: center;
  }
</style>
