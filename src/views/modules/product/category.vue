<template>
  <div>
    <el-tree
      :data="data"
      :props="defaultProps"
      :expand-on-click-node="false"
      show-checkbox
      node-key="catId"
      :default-expanded-keys="expandIds"
      @node-click="handleNodeClick"
    >
      <span class="custom-tree-node" slot-scope="{ node, data }">
        <span>{{ node.label }}</span>
        <span>
          <el-button
            v-if="node.level <=2"
            type="text"
            size="mini"
            @click="() => append(data)"
          >Append</el-button>
          <el-button
            v-if="node.childNodes.length==0"
            type="text"
            size="mini"
            @click="() => remove(node, data)"
          >Delete</el-button>
        </span>
      </span>
    </el-tree>
  </div>
</template>

<script>
export default {
  components: {},
  props: {},
  data() {
    return {
      data: [],
      expandIds:[],
      defaultProps: {
        children: "children",
        label: "name",
      },
    };
  },
  // 方法
  methods: {
    handleNodeClick(data) {},
    // 获取数据列表
    getAllMenus() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/product/category/listTree"),
        method: "get",
      }).then(({ data }) => {
        this.data = data.tree;
      });
    },
    // 提交方法
    append(data) {
      console.log(data);
    },
    // 删除方法
    remove(node, data) {
      console.log(node);
      this.$confirm(`是否将永久删【${data.name}】菜单?`, "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          var id = data.catId;
          this.$http({
            url: this.$http.adornUrl("/product/category/" + id),
            method: "delete",
          }).then(({ data }) => {
            this.$message({
              message: "菜单删除成功",
              type: "success",
            });
            this.getAllMenus();
            this.expandIds=[node.parent.data.catId]
          });
        })
        .catch(() => {});
    },
  },
  //计算属性
  computed: {},
  //监控data中数据变化
  watch: {},
  //声明周期 - 创建完成（可以访问当前this实例）
  created() {
    this.getAllMenus();
  },
  //生命周期 - 挂载完成（可以访问DOM元素）
  mounted() {},
  beforeCreate() {}, //生命周期 - 创建之前
  beforeMount() {}, //生命周期 - 挂载之前
  beforeUpdate() {}, //声明周期 - 更新之前
  updated() {}, //生命周期 - 更新之后
  beforeDestroy() {}, //生命周期 - 销毁之前
  destroyed() {}, //生命周期 - 销毁之后
  activated() {}, //缓存keep-alive
};
</script>

<style scoped>
</style>