<style lang="less"></style>
<template>
  <div class="h-panel w-1000">
    <div class="h-panel-bar">
      <span class="h-panel-title">编辑</span>
    </div>
    <div class="h-panel-body">
      <Form
        mode="block"
        ref="form"
        :validOnChange="true"
        :showErrorTip="true"
        :labelWidth="110"
        :rules="rules"
        :model="role"
      >
        <Row :space="10">
          <Cell :width="6">
            <FormItem label="权限" prop="permission_ids">
              <Select
                v-model="role.permission_ids"
                :datas="permissions"
                :filterable="true"
                :multiple="true"
              ></Select>
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="角色名" prop="display_name">
              <input type="text" v-model="role.display_name" />
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="描述" prop="description">
              <input type="text" v-model="role.description" />
            </FormItem>
          </Cell>
        </Row>

        <FormItem>
          <Button color="primary" @click="save">保存</Button>
        </FormItem>
      </Form>
    </div>
  </div>
</template>
<script>
import Role from 'model/AdministratorRole';

export default {
  props: ['id'],
  data() {
    return {
      role: Role.parse({}),
      rules: {
        required: ['display_name', 'description']
      },
      permissions: []
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      R.AdministratorRole.Edit({ id: this.id }).then(resp => {
        this.role = resp.data;
      });

      R.AdministratorRole.Create().then(res => {
        let data = res.data.permissions;
        let permissions = [];
        for (let key in data) {
          permissions.push({
            title: key,
            isLabel: true
          });
          data[key].forEach(item => {
            permissions.push({
              title: item.display_name,
              key: item.id
            });
          });
        }
        this.permissions = permissions;
      });
    },
    save() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        this.$emit('success', this.role);
      }
    }
  }
};
</script>
