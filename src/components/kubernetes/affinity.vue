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
          @click="addRequiredSetting"
          >新增</el-button
        >
      </el-form-item>
      <div
        v-for="(item, index) in state.affinity.requiredDuringSchedulingIgnoredDuringExecution"
        :key="index"
        style="margin-left: 100px"
      >
        <el-card shadow="hover" style="margin-bottom: 10px; width: 80vh; background: transparent">
          <div style="display: flex; justify-content: space-between">
            <div style="width: 100%">
              <el-form-item label="命名空间" label-position="top" style="width: 350px">
                <el-select size="small">
                  <el-option label="aaa" value="aa" />
                </el-select>
              </el-form-item>
              <el-form-item
                label="拓扑域"
                :prop="'requiredDuringSchedulingIgnoredDuringExecution.' + index + '.topologyKey'"
                :rules="[{ required: true, message: '不能为空', trigger: 'blur' }]"
                style="width: 350px"
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
                    @click="addMatchExpressions(item)"
                    >新增</el-button
                  ><el-button size="small" link type="primary" @click="handleAppDialog"
                    >查看应用列表</el-button
                  >
                </div>
              </el-form-item>
              <el-form-item>
                <el-table size="small" :data="item.labelSelector.matchExpressions">
                  <el-table-column prop="key" label="标签名">
                    <template #default="scope">
                      <el-form-item
                        :prop="
                          'requiredDuringSchedulingIgnoredDuringExecution.' +
                          index +
                          '.labelSelector.matchExpressions.' +
                          scope.$index +
                          '.key'
                        "
                        :rules="[{ required: true, message: '不能为空', trigger: 'blur' }]"
                      >
                        <el-input v-model="scope.row.key" size="small" />
                      </el-form-item>
                    </template>
                  </el-table-column>
                  <el-table-column label="操作符号" width="150">
                    <template #default="scope">
                      <el-form-item>
                        <el-select v-model="scope.row.operator" size="small">
                          <el-option label="In" value="In" />
                          <el-option label="NotIn" value="NotIn" />
                          <el-option label="Exists" value="Exists" />
                          <el-option label="DoesNotExists" value="DoesNotExists" />
                        </el-select>
                      </el-form-item>
                    </template>
                  </el-table-column>
                  <el-table-column label="标签值">
                    <template #default="scope">
                      <el-form-item
                        :prop="
                          'requiredDuringSchedulingIgnoredDuringExecution.' +
                          index +
                          '.labelSelector.matchExpressions.' +
                          scope.$index +
                          '.values'
                        "
                        :rules="[
                          { required: true, validator: checkExpressionValues, trigger: 'blur' },
                        ]"
                      >
                        <el-input v-model="scope.row.values" size="small" />
                      </el-form-item>
                    </template>
                  </el-table-column>
                  <el-table-column width="80">
                    <template #default="scope">
                      <el-form-item>
                        <el-button
                          icon="RemoveFilled"
                          type="primary"
                          size="small"
                          text
                          style="margin-left: 10px"
                          @click="item.labelSelector.matchExpressions.splice(scope.$index, 1)"
                        ></el-button>
                      </el-form-item>
                    </template>
                  </el-table-column>
                </el-table>
              </el-form-item>
            </div>
            <div>
              <el-button
                icon="Delete"
                type="danger"
                size="small"
                text
                @click="deleteRequiredSetting(index)"
                >删除</el-button
              >
            </div>
          </div>
        </el-card>
      </div>
      <el-form-item label="尽量满足">
        <el-button
          icon="CirclePlusFilled"
          type="primary"
          size="small"
          style="padding-left: 0"
          text
          @click="addPreferredSetting"
          >新增</el-button
        >
      </el-form-item>
      <div
        v-for="(item, index) in state.affinity.preferredDuringSchedulingIgnoredDuringExecution"
        :key="index"
        style="margin-left: 100px"
      >
        <el-card shadow="hover" style="margin-bottom: 10px; width: 80vh; background: transparent">
          <div style="display: flex; justify-content: space-between">
            <div style="width: 100%">
              <el-form-item label="命名空间" label-position="top" style="width: 350px">
                <el-select size="small">
                  <el-option label="aaa" value="aa" />
                </el-select>
              </el-form-item>
              <el-form-item
                label="拓扑域"
                :prop="
                  'preferredDuringSchedulingIgnoredDuringExecution' +
                  index +
                  '.podAffinityTerm.topologyKey'
                "
                :rules="[{ required: true, message: '不能为空', trigger: 'blur' }]"
                style="width: 350px"
              >
                <el-input v-model="item.podAffinityTerm.topologyKey" size="small" />
              </el-form-item>
              <el-form-item style="width: 300px">
                <div style="display: flex; align-items: center; width: 200px">
                  <span>选择器</span>
                  <el-button
                    icon="CirclePlusFilled"
                    type="primary"
                    size="small"
                    text
                    @click="addMatchExpressions(item.podAffinityTerm)"
                    >新增</el-button
                  ><el-button size="small" link type="primary" @click="handleAppDialog"
                    >查看应用列表</el-button
                  >
                </div>
              </el-form-item>
              <el-form-item>
                <el-table
                  size="small"
                  :data="item.podAffinityTerm.labelSelector.matchExpressions"
                  :row-style="() => ({ height: '50px' })"
                >
                  <el-table-column prop="key" label="标签名">
                    <template #default="scope">
                      <el-form-item
                        :prop="
                          'preferredDuringSchedulingIgnoredDuringExecution.' +
                          index +
                          '.podAffinityTerm.labelSelector.matchExpressions.' +
                          scope.$index +
                          '.key'
                        "
                        :rules="[{ required: true, message: '不能为空', trigger: 'blur' }]"
                      >
                        <el-input v-model="scope.row.key" size="small" />
                      </el-form-item>
                    </template>
                  </el-table-column>
                  <el-table-column label="操作符号" width="150">
                    <template #default="scope">
                      <el-form-item>
                        <el-select v-model="scope.row.operator" size="small">
                          <el-option label="In" value="In" />
                          <el-option label="NotIn" value="NotIn" />
                          <el-option label="Exists" value="Exists" />
                          <el-option label="DoesNotExists" value="DoesNotExists" />
                        </el-select>
                      </el-form-item>
                    </template>
                  </el-table-column>
                  <el-table-column label="标签值">
                    <template #default="scope">
                      <el-form-item
                        v-if="scope.row.operator === 'In' || scope.row.operator === 'NotIn'"
                        :prop="
                          'preferredDuringSchedulingIgnoredDuringExecution.' +
                          index +
                          '.podAffinityTerm.labelSelector.matchExpressions.' +
                          scope.$index +
                          '.values'
                        "
                        :rules="[
                          { required: true, validator: checkExpressionValues, trigger: 'blur' },
                        ]"
                      >
                        <el-input v-model="scope.row.values" size="small" />
                      </el-form-item>
                    </template>
                  </el-table-column>
                  <el-table-column width="80">
                    <template #default="scope">
                      <el-form-item>
                        <el-button
                          icon="RemoveFilled"
                          type="primary"
                          size="small"
                          text
                          style="margin-left: 10px"
                          @click="
                            item.podAffinityTerm.labelSelector.matchExpressions.splice(
                              scope.$index,
                              1,
                            )
                          "
                        ></el-button>
                      </el-form-item>
                    </template>
                  </el-table-column>
                </el-table>
              </el-form-item>
              <el-form-item
                label="权重"
                :prop="'preferredDuringSchedulingIgnoredDuringExecution.' + index + '.weight'"
                :rules="[{ required: true, message: '不能为空', trigger: 'blur' }]"
              >
                <el-input-number v-model="item.weight" :min="1" :max="100" size="small" />
              </el-form-item>
            </div>
            <div>
              <el-button
                icon="Delete"
                type="danger"
                size="small"
                text
                @click="deletePreferredSetting(index)"
                >删除</el-button
              >
            </div>
          </div>
        </el-card>
      </div>
    </div>
  </el-form>
  <AppDialog v-if="state.appDialogVisible" v-model:visible="state.appDialogVisible" />
</template>
<script setup>
import { defineAsyncComponent, reactive } from 'vue';

const AppDialog = defineAsyncComponent(() => import('./affinityAppDialog.vue'));
const state = reactive({
  appDialogVisible: false,
  verified: true,
  set: false,
  affinity: {
    preferredDuringSchedulingIgnoredDuringExecution: [],
    requiredDuringSchedulingIgnoredDuringExecution: [],
  },
});

const set = () => {
  state.set = !state.set;
};
const cancel = () => {
  state.set = !state.set;
};

const handleAppDialog = () => {
  state.appDialogVisible = true;
};
const addRequiredSetting = () => {
  state.affinity.requiredDuringSchedulingIgnoredDuringExecution.push({
    labelSelector: {
      matchExpressions: [],
    },
    topologyKey: 'kubernetes.io/hostname',
  });
};

const deleteRequiredSetting = (index) => {
  state.affinity.requiredDuringSchedulingIgnoredDuringExecution.splice(index, 1);
};
const deletePreferredSetting = (index) => {
  state.affinity.preferredDuringSchedulingIgnoredDuringExecution.splice(index, 1);
};
const addPreferredSetting = () => {
  state.affinity.preferredDuringSchedulingIgnoredDuringExecution.push({
    podAffinityTerm: {
      labelSelector: {
        matchExpressions: [],
      },
      topologyKey: 'kubernetes.io/hostname',
    },
    weight: 100,
  });
};
const addMatchExpressions = (data) => {
  data.labelSelector.matchExpressions.push({
    key: '',
    operator: 'In',
    values: [''],
  });
};

const checkExpressionValues = (rule, value, callback) => {
  if (value.length === 1 && value[0] === '') {
    callback(new Error('不能为空'));
  } else {
    callback();
  }
};
const props = defineProps({
  title: {
    type: String,
    required: true,
  },
});
</script>

<style scoped></style>
