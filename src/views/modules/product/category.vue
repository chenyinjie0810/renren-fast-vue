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
          <el-button v-if="node.level <=2" type="text" size="mini" @click="() => edit(data)">Edit</el-button>
          <el-button
            v-if="node.childNodes.length==0"
            type="text"
            size="mini"
            @click="() => remove(node, data)"
          >Delete</el-button>
        </span>
      </span>
    </el-tree>
    <!-- 表单 -->
    <el-dialog title="提示" :visible.sync="dialogVisible" width="30%" :close-on-click-modal="false">
      <el-form ref="form" :model="category" label-width="80px">
        <el-form-item label="分类名称">
          <el-input v-model="category.name"></el-input>
        </el-form-item>
        <el-form-item label="排序号">
          <el-input v-model="category.sort"></el-input>
        </el-form-item>
        <el-form-item label="图标">
          <el-input v-model="category.icon"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="submit">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  components: {},
  props: {},
  data() {
    return {
      dialogType: "",
      category: {
        name: null,
        parentCid: null,
        catLevel: null,
        showStatus: null,
        sort: null,
        productCount: null,
        icon: null
      },
      dialogVisible: false,
      data: [],
      expandIds: [],
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
    submit() {
      debugger
      if (this.dialogType == "add") {
        this.addCategory();
      } else if (this.dialogType == "upd") {
        this.editCategory();
      }
    },
    // 提交方法
    append(data) {
      this.category.catId = null;
      this.category.name = null;
      this.category.sort = null;
      this.category.icon = null;
      this.category.sort = null;
      this.dialogType = "add";
      this.dialogVisible = true;
      this.category.parentCid = data.catId;
      this.category.catLevel = data.catLevel * 1 + 1;
      console.log(data);
    },
    //添加分类
    addCategory() {
      console.log("提交的三级分类," + this.category);
      this.$http({
        url: this.$http.adornUrl("/product/category/save"),
        method: "post",
        data: this.$http.adornData(this.category, false),
      }).then(({ data }) => {
        this.$message({
          message: "菜单添加成功",
          type: "success",
        });
        this.dialogVisible = false;
        this.getAllMenus();
        this.expandIds = [this.category.parentCid];
      });
    },
    //打开修改分类页
    edit(data) {
      this.dialogType = "upd";
      var catId = data.catId;
      this.$http({
        url: this.$http.adornUrl(`/product/category/${catId}`),
        method: "get",
      }).then(({ data }) => {
        if (data.code == 0) {
          this.dialogVisible = true;
          this.category.catId = data.category.catId;
          this.category.name = data.category.name;
          this.category.parentCid = data.category.parentCid;
          this.category.catLevel = data.category.catLevel;
          this.category.sort = data.category.sort;
          this.category.icon = data.category.icon;
          this.category.sort = data.category.sort;
        }
      });
    },
    //提交修改
    editCategory() {
      this.$http({
        url: this.$http.adornUrl("/product/category/update"),
        method: "post",
        data: this.$http.adornData(this.category, false),
      }).then(({ data }) => {
        console.log(data);
        if (data.code == 0) {
           this.$message({
              message: "菜单修改成功",
              type: "success",
            });
            this.dialogVisible = false;
            this.getAllMenus();
            this.expandIds = [data.category.catId];
        }
      });
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
            this.expandIds = [node.parent.data.catId];
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