<template>
  <el-card class="app-container" shadow="always">

    <el-form ref="form1" :model="form1" :rules="rules2" label-width="120px">
      <el-form-item
        prop="username"
        label="用户名">
        <el-input v-model="form1.username" placeholder="请输入用户名，用作登录，不可修改"/>
      </el-form-item>
      <el-form-item
        label="密码"
        prop="password"
      >
        <el-input v-model="form1.password" type="password" placeholder="请输入密码"/>
      </el-form-item>
      <el-form-item
        label="确认密码"
        prop="pass"
      >
        <el-input v-model="form1.pass" type="password" placeholder="请再次输入密码"/>
      </el-form-item>
      <el-form-item
        label="姓名"
        prop="name"
      >
        <el-input v-model.number="form1.name" placeholder="请输入真实姓名"/>
      </el-form-item>
      <el-form-item
        prop="gender"
        label="性别">
        <el-radio v-model="form1.gender" label="男">男</el-radio>
        <el-radio v-model="form1.gender" label="女">女</el-radio>
      </el-form-item>
      <el-form-item
        label="手机号码"
        prop="phone"
      >
        <el-input v-model.number="form1.phone" type="number" placeholder="请输入手机号码" style="width: 50%"/>
<!--        <el-button type="primary" disabled="">发送验证码</el-button>-->
      </el-form-item>
      <el-form-item
        :rules="[
          { required: true, message: '不能为空'}
        ]"
        label="所属部门">
        <el-select v-model="form1.deptId"  placeholder="请选择所属部门">
          <el-option v-for="t in typeList" :label="t.department" :value="t.id" :key="t.id">
            {{ t.department }}
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item
        label="邮箱地址"
        prop="email"
      >
        <el-input v-model="form1.email"/>
      </el-form-item>
      <el-form-item
        label="住宅地址"
        prop="address"
      >
        <el-input v-model="form1.address"/>
      </el-form-item>
      <el-form-item>
        <el-button :loading="loading" type="primary" @click="onSubmit()">提交</el-button>
        <el-button @click="onCancel">取消</el-button>
      </el-form-item>
    </el-form>
  </el-card>
</template>
<script>
  import { add,getAllDept } from '../../api/worker'

  import ElCard from 'element-ui/packages/card/src/main'

  export default {
    components: { ElCard },
    data() {
      var validatePass = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请再次输入密码'))
        } else if (value !== this.form1.password) {
          callback(new Error('两次输入密码不一致!'))
        } else {
          callback()
        }
      }
      return {
        form1: {
          username: null,
          password: null,
          pass: null,
          name: null,
          phone: null,
          email: null,
          deptId: null,
          address:null,
          gender: null
        },
        loading: false,
        typeList: null,
        rules2: {
          username: [
            { required: true, message: '不能为空' }
          ],
          password: [
            { required: true, message: '不能为空' }
          ],
          pass: [
            { required: true, validator: validatePass, trigger: 'blur' }
          ],
          name: [
            { required: true, message: '不能为空' }
          ],
          phone: [
            { required: true, message: '不能为空' }
          ],
          gender: [
            { required: true, message: '不能为空' }
          ]
        }
      }
    },
    created: function() {
      this.fetchData()
    },
    methods: {
      fetchData() {
        getAllDept().then(res => {
          console.log(res)
          this.typeList = res.data
        })
      },
      onSubmit() {
        this.$refs.form1.validate((valid) => {
          if (valid) {
            this.loading = true
            add(this.form1).then(response => {
              const res = response
              if (res.code === 1000) {
                this.$message({
                  message: '提交成功！',
                  type: 'success'
                })
                this.loading = false
                setTimeout(this.onCancel(), 20000)
              } else {
                this.showError(res.message)
                this.loading = false
              }
            }).catch(error => {
              this.loading = false
              this.showError(error)
            })
          } else {
            this.loading = false
            return false
          }
        })
      },
      showError(msg) {
        this.$message({
          message: msg,
          type: 'error'
        })
      },
      onCancel() {
        this.$router.back()
      }
    }
  }
</script>

<style scoped>
  .line {
    text-align: center;
  }
</style>
