<template>
  <div class="budget-list-wrap">
    <ElCard :header="header">
      <template v-if="!isEmpty">
        <div class="list-item" v-for="(item, prop) in list" :key="prop">
          <template v-if="!item.isEdited">
            <span class="budget-comment">{{ item.comment }}</span>
            <span
              class="budget-value"
              :class="{'budget-value-positive': item.value > 0,'budget-value-negative': item.value < 0}"
            >{{ item.value }}</span>
            <ElButton type="primary" icon="el-icon-edit" circle size="mini" @click="editItem(item)"></ElButton>
            <ElButton
              type="danger"
              icon="el-icon-delete"
              circle
              size="mini"
              @click="deleteItem(item.id)"
            ></ElButton>
          </template>
          <template v-else>
            <ElForm
              class="edit-form"
              :model="editedItem"
              ref="editItemForm"
              :rules="rules"
              lable-position="top"
            >
              <ElFormItem class="edit-comment" prop="comment">
                <ElInput v-model="editedItem.comment" />
              </ElFormItem>
              <ElFormItem class="edit-value" prop="value">
                <ElInput v-model.number="editedItem.value" />
              </ElFormItem>

              <ElButton
                type="success"
                icon="el-icon-check"
                circle
                size="mini"
                @click="editAply(item)"
              ></ElButton>
              <ElButton
                type="danger"
                icon="el-icon-delete"
                circle
                size="mini"
                @click="editCancel(item)"
              ></ElButton>
            </ElForm>
          </template>
        </div>
      </template>
      <ElAlert v-else type="info" :title="emptyTitle" :closable="false" />
    </ElCard>
  </div>
</template>

<script>
function customNumValidator(rule, value, callback) {
  if (value === 0) {
    return callback(new Error("Число должно быть больше нуля"));
  }
  callback();
}

export default {
  name: "BudgetList",
  props: {
    list: {
      type: Object,
      default: () => ({})
    }
  },
  data: () => ({
    header: "Budget List",
    emptyTitle: "Empty List",
    editedItem: {
      comment: "",
      value: null
    },
    rules: {
      comment: [
        { required: true, message: "Please input comment", trigger: "change" }
      ],
      value: [
        { required: true, message: "Please input value", trigger: "change" },
        {
          type: "number",
          message: "Value must be a number",
          trigger: "change"
        },
        { validator: customNumValidator, trigger: "change" }
      ]
    }
  }),
  computed: {
    isEmpty() {
      return !Object.keys(this.list).length;
    }
  },
  methods: {
    deleteItem(id) {
      this.$emit("deleteItem", id);
    },
    editItem(item) {
      this.editedItem.comment = item.comment;
      this.editedItem.value = item.value;
      item.isEdited = true;
    },
    editAply(item) {
      this.$refs.editItemForm["0"].validate(valid => {
        if (valid) {
          item.comment = this.editedItem.comment;
          item.value = this.editedItem.value;
          item.isEdited = false;
        }
      });
    },
    editCancel(item) {
      item.isEdited = false;
    }
  }
};
</script>

<style scoped>
.budget-list-wrap {
  max-width: 600px;
  margin: auto;
}

.list-item {
  display: flex;
  align-items: center;
  padding: 10px 15px;
}
.budget-value {
  font-weight: bold;
  margin-left: auto;
  margin-right: 20px;
}
.budget-value-positive {
  color: green;
}
.budget-value-negative {
  color: red;
}
.budget-comment {
  max-width: 350px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
.edit-comment {
  margin-right: 20px;
  width: 100%;
}
.edit-value {
  margin-right: 20px;
}
.edit-form {
  display: flex;
}
.el-button.is-circle {
  padding: 12px;
}
.el-form-item {
  margin-bottom: 0;
}
</style>
