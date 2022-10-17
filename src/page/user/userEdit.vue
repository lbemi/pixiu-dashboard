<template>
  <!-- 修改用户信息 -->
  <el-dialog v-model="data.updateUserVisible" style="color: #000000; font: 14px" width="360px" center
    @close="data.updateUserVisible = false">
    <template #title>
      <div style="text-align: left; font-weight: bold; padding-left: 5px">
        修改用户
      </div>
    </template>
    <el-form :label-position="labelPosition" label-width="100px" :model="scope.row" style="max-width: 260px">
      <el-form-item label="名字:" required>
        <el-input v-model="data.userForm.name" />
      </el-form-item>
      <el-form-item label="描述:">
        <el-input v-model="data.userForm.description" required="" />
      </el-form-item>
      <el-form-item label="邮箱">
        <el-input v-model="data.userForm.email" />
      </el-form-item>
      <el-form-item label="状态">
        <el-switch v-model="data.userForm.status" class="ml-2"
          style="--el-switch-on-color: #409EFF; --el-switch-off-color: #ff4949" :active-value="1" :inactive-value="0"
          size="large" inline-prompt active-text="启用" inactive-text="禁用" />
      </el-form-item>

      <el-form-item label="密码" required>
        <el-input v-model="data.userForm.password" type="password" show-password />
      </el-form-item>
      <el-form-item label="再次输入密码" required>
        <el-input v-model="data.confirmPassword" type="password" show-password />
      </el-form-item>
    </el-form>

    <template #footer>
      <span class="dialog-footer">
        <el-button @click="data.updateUserVisible = false">取消</el-button>
        <el-button type="primary" @click="confirmUpdateUser()">创建</el-button>
      </span>
    </template>
  </el-dialog>
</template>

<script setup >
const data = reactive({
  updateUserVisible: false,
  userForm: {
    description: "",
    email: "",
    name: "",
    password: "",
    status: 1
  }
});

const confirmUpdateUser = async () => {
  
  console.log("--",data.userForm);
  // const resp = await proxy.$http({
  //   method: "put",
  //   url: "/users/{id}",
  // });
  // data.updateUserVisible = false
  // if (resp.code === 200) {
  //   getUserList();
  //   ElMessage({
  //     type: "success",
  //     message: "添加成功",
  //   });
  // } else {
  //   ElMessage({
  //     type: "error",
  //     message: "添加失败",
  //   });
  // }
}
</script>

