<template>
  <el-form
    :model="state.affinity"
    label-width="100px"
    status-icon
    label-position="left"
    require-asterisk-position="right"
  >
    <el-form-item :label="title">
      <el-button
        v-if="!state.set"
        icon="Edit"
        type="primary"
        size="small"
        text
        style="padding-left: 0"
        @click="set"
        >配置</el-button
      >
      <el-button
        v-else
        icon="Delete"
        type="primary"
        size="small"
        text
        style="padding-left: 0"
        @click="cancel"
        >取消配置</el-button
      ></el-form-item
    >
    <div v-show="state.set" style="padding-left: 100px">
      <el-form-item label="必须满足">
        <el-button
          icon="CirclePlusFilled"
          type="primary"
          size="small"
          style="padding-left: 0"
          text
          @click="state.env.push({ type: 'custom', name: '', value: '', otherValue: '' })"
          >新增</el-button
        >
      </el-form-item>
      <div
        v-for="(item, index) in state.affinity.requiredDuringSchedulingIgnoredDuringExecution"
        :key="index"
        style="margin-left: 100px"
      >
        <el-form-item label="命名空间" label-position="top" style="width: 350px">
          <el-select size="small">
            <el-option label="aaa" value="aa" />
          </el-select>
        </el-form-item>
        <el-form-item
          label="拓扑域"
          :prop="'requiredDuringSchedulingIgnoredDuringExecution.' + index + '.topologyKey'"
          :rules="[{ required: true, message: '不能为空', trigger: 'blur' }]"
        >
          <el-input v-model="item.topologyKey" size="small" />
        </el-form-item>
        <el-form-item style="width: 300px">
          <div style="display: flex; align-items: center; width: 200px">
            <span>选择器</span>
            <el-button
              icon="CirclePlusFilled"
              type="primary"
              size="small"
              text
              @click="state.env.push({ type: 'custom', name: '', value: '', otherValue: '' })"
              >新增</el-button
            ><el-button size="small" link type="primary">查看应用列表</el-button>
          </div>
        </el-form-item>
        <el-form-item>
          <el-table size="small" :data="item.matchExpressions">
            <el-table-column label="标签名">
              <template #default="scope">
                <el-form-item>
                  <el-input v-model="scope.row.key" size="small" />
                </el-form-item>
              </template>
            </el-table-column>
            <el-table-column prop="operator" label="操作符号">
              <el-form-item>
                <template #default="scope">
                  <el-select v-model="scope.row.operator" size="small">
                    <el-option label="In" value="In" />
                    <el-option label="NotIn" value="NotIn" />
                    <el-option label="Exists" value="Exists" />
                    <el-option label="DoesNotExists" value="DoesNotExists" />
                  </el-select>
                </template>
              </el-form-item>
            </el-table-column>
            <el-table-column label="标签值"></el-table-column>
          </el-table>
        </el-form-item>
      </div>
      <el-form-item label="尽量满足"> 尽量满足 </el-form-item>
    </div>
  </el-form>
</template>
<script setup>
import { reactive } from 'vue';

const state = reactive({
  verified: true,
  set: false,
  affinity: {
    preferredDuringSchedulingIgnoredDuringExecution: [
      {
        podAffinityTerm: {
          labelSelector: {
            matchExpressions: [
              {
                key: 'service',
                operator: 'In',
                values: ['oilyy-psp-service-uat'],
              },
            ],
          },
          topologyKey: 'kubernetes.io/hostname',
        },
        weight: 100,
      },
    ],
    requiredDuringSchedulingIgnoredDuringExecution: [
      {
        labelSelector: {
          matchExpressions: [
            {
              key: 'service',
              operator: 'In',
              values: ['oilyy-dpc-cservice-uat'],
            },
          ],
        },
        topologyKey: 'kubernetes.io/hostname',
      },
    ],
  },
});

const set = () => {
  state.set = !state.set;
};
const cancel = () => {
  state.set = !state.set;
};
const props = defineProps({
  title: {
    type: String,
    required: true,
    default: '阿阿斯顿',
  },
});
</script>

<style scoped></style>
