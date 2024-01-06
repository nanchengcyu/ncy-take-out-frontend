<template>
  <div class='dashboard-container'>
    <div class='container'>
      <div>
        <label style='margin-right: 5px'>
          员工姓名：
        </label>
        <input v-model='name' placeholder='请输入员工姓名' style='width: 15%' />
        <button type='button' style='margin-left: 20px' @click=' pageQuery()'>查询</button>
        <button type='button' style='float: right'>+添加员工</button>
      </div>
      <table>
        <column label='操作'>
          <template>

          </template>
        </column>


      </table>
      <el-pagination
        @size-change='handleSizeChange'
        @current-change='handleCurrentChange'
        :page-sizes='[10, 20, 30, 40]'
        :page-size='pageSize'
        layout='total, sizes, prev, pager, next, jumper'
        :total='400'>
      </el-pagination>
    </div>

  </div>
</template>
<script lang='ts'>
import { getEmployeeList, enableOrDisableEmployee } from '@/api/employee'


export default {
  //模型数据
  data() {
    return {
      name: '', //员工姓名，对应上面的输入框 v-model双向绑定
      page: 1,
      pageSize: 10,
      total: 0,
      records: [] //当前页面展示的数据集合
    }
  },
  created() {
    this.pageQuery() //vue的生命周期函数，在跳转到当前页面时，发起一次查询操作 防止当前 页面为空
  },
  methods: {
    //分页查询
    pageQuery() {
      //alert("员工查询")//此处可以先校验一下是否成功绑定了事件
      //准备请求参数
      const params = { name: this.name, page: this.page, pageSize: this.pageSize }
      //发送ajax请求，访问后端服务，获取分页数据，但是一般不写在这个地方  按照项目规范一般是封装到api中的ts文件中
      //getEmployeeList({name:'nanchengyu',page:1,pageSize:10})//此处可以先写死，做测试
      getEmployeeList({ params }).then(res => {
        if (res.data.code === 1) {
          this.total = res.data.data.total //第一个data和res是一个整体
          this.records = res.date.data.records
        }
      }).catch(err => {
        this.$message.error('请求出错了：' + err.message)
      })

    },
    //pageSize发生变化时触发
    handleSizeChange(pageSize) {
      this.pageSize = pageSize
      this.pageQuery()

    },
    handleCurrentChange(page) {
      this.page = page
      this.pageQuery() //改变page 重新调用pageQuery查询数据

    },
    handleStartOrStop(row) {
      if (row.username === 'admin'){
        this.$message.error('admin为系统的管理员账号，不能更改')
        return //直接返回，不能继续下面操作
      }
      //弹出确认提示框
      this.$confirm('确认要修改当前员工账号的状态吗?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        const p = {
          id: row.id,
          status: !row.status ? 1 : 0
        }
        enableOrDisableEmployee(p).then(res => {
          if (res.data.code === 1) {
            this.$message.success('员工状态修改成功')
            this.pageQuery()
          }
        })
      })

    }

  }

}
</script>

<style lang='scss' scoped>
<
link rel

=
"stylesheet"
href

=
"https://unpkg.com/element-ui/lib/theme-chalk/index.css"
>
.disabled-text {
  color: #bac0cd !important;
}
</style>
