<template>
  <div>
      <el-tree :data="data" :props="defaultProps" @node-click="handleNodeClick"></el-tree>
  </div>
</template>

<script>
export default {
  //import 引入的组件需要注入到对象中才能使用
  components: {},
  props: {},
     data() {
      return {
        data: [],
        defaultProps: {
          children: 'children',
          label: 'label'
        }
      };
    },
    //方法
    methods: {
      handleNodeClick(data) {
        console.log(data);
      },
      // 获取数据列表
      getAllMenus () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/sys/config/list'),
          method: 'get',
        }).then(({data}) => {
            console.log(data);
        })
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