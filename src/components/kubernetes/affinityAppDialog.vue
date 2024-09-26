<template>
  <el-dialog
    v-model="state.dialogVisible"
    title="查看应用列表"
    width="500"
    :before-close="handleClose"
  >
    <span>This is a message</span>
    <template #footer>
      <div class="dialog-footer">
        <el-button @click="handleClose">Cancel</el-button>
        <el-button type="primary" @click="handleClose"> Confirm </el-button>
      </div>
    </template>
  </el-dialog>
</template>
<script setup>
import { onMounted, reactive } from 'vue';

const state = reactive({
  dialogVisible: false,
});

const emit = defineEmits(['update:visible']);
const handleClose = () => {
  emit('update:visible', false);
};
const props = defineProps({
  visible: {
    type: Boolean,
    required: true,
  },
});
onMounted(() => {
  state.dialogVisible = props.visible;
});
</script>
<style scoped></style>
