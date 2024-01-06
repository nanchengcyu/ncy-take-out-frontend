<template>
  <div class='addBrand-container'>
    <div class='container'>
      <el-form :model='ruleForm' :rules='rules' ref='ruleForm' label-width='180px'>
        <el-form-item label='账号' prop='username'>
          <el-input v-model='ruleForm.username'></el-input>
        </el-form-item>
        <el-form-item label='员工姓名' prop='name'>
          <el-input v-model='ruleForm.name'></el-input>
        </el-form-item>
        <el-form-item label='手机号' prop='phone'>
          <el-input v-model='ruleForm.phone'></el-input>
        </el-form-item>
        <el-form-item label='性别' prop='sex'>
          <el-radio v-model='ruleForm.sex' label='1'>男</el-radio>
          <el-radio v-model='ruleForm.sex' label='2'>女</el-radio>
        </el-form-item>
        <el-form-item label='身份证号' prop='idNumber'>
          <el-input v-model='ruleForm.idNumber'></el-input>
        </el-form-item>
        <div class='subBox'>
          <el-button type='primary' @click="submitForm('ruleForm',false)">保存</el-button>
          <el-button
            v-if="this.optType === 'add'"
            type='primary'
            @click="submitForm('ruleForm',true)">保存并继续添加员工
          </el-button>
          <el-button @click="() => this.$router.push('/employee')">返回</el-button>
        </div>
      </el-form>
    </div>
  </div>
</template>

<script lang='ts'>
import { addEmployee,queryEmployeeById } from '@/api/employee'

export default {
  data() {
    return {
      //optType :'add',//当前的操作类型，新增还是修改
      optType:'',
      ruleForm: {
        name: '',
        username: '',
        sex: '1',
        phone: '',
        idNumber: ''
      },
      rules: {
        name: [
          { required: true, message: '请输入员工名称', trigger: 'blur' }

        ],
        username: [
          { required: true, message: '请输入员工账号', trigger: 'blur' }

        ]

      }
    }
  },
  created(){
   //获取路由参数（id）如果有则为修改参数，无则为添加操作
    this.optType = this.$route.query.id ?'update':'add'
    if (this.optType === 'update'){
      //修改操作，需要查询员工信息用于页面回显
      queryEmployeeById(this.$route.query.id).then(res =>{
        if (res.data.code === 1){
          this.ruleForm = res.data.data
        }
      })
    }
  },
  methods: {
    submitForm(formName, isContinue) {
      // 进行表单检验
      this.$refs[formName].validate((valid) => {
        if (valid) {
          // 表单校验通过，发起Ajax请求，将数据提交到后端

          addEmployee(this.ruleForm).then((res) => {
            if (res.data.code === 1) {
              this.$message.success('员工添加成功！')

              if (isContinue) {
                // 如果 isContinue 为 true，则清空表单数据
                this.ruleForm = {
                  name: '',
                  username: '',
                  sex: '1',
                  phone: '',
                  idNumber: ''
                }
              } else {
                // 如果 isContinue 为 false，则跳转到 '/employee' 路由
                this.$router.push('/employee')
              }
            } else {
              // 如果请求返回的状态码不是 1，则显示错误消息
              this.$message.error(res.data.msg)
            }
          })
        }
      })
    }
  }


}
</script>

<style lang='scss' scoped>
.addBrand {
  &-container {
    margin: 30px;
    margin-top: 30px;

    .HeadLable {
      background-color: transparent;
      margin-bottom: 0px;
      padding-left: 0px;
    }

    .container {
      position: relative;
      z-index: 1;
      background: #fff;
      padding: 30px;
      border-radius: 4px;
      // min-height: 500px;
      .subBox {
        padding-top: 30px;
        text-align: center;
        border-top: solid 1px $gray-5;
      }
    }

    .idNumber {
      margin-bottom: 39px;
    }

    .el-form-item {
      margin-bottom: 29px;
    }

    .el-input {
      width: 293px;
    }
  }
}
</style>
