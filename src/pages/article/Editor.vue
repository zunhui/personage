<template>
    <div class="article_editor">
        <!-- 按钮 -->
        <div class="btns">
            <el-button type="text" @click="back">返回</el-button>
        </div>
        <!-- 表单 -->
        <el-form ref="form" :model="form" label-width="80px">
            <el-form-item label="所属栏目">
                <el-select clearable="" v-model="form.categoryId" placeholder="请选择所属栏目">
                  <el-option v-for="c in categorys" :key="c.id" :label="c.name" :value="c.id"></el-option>
                </el-select>
            </el-form-item>
            <el-form-item label="文章标题">
                <el-input v-model="form.title"></el-input>
            </el-form-item>
            <el-form-item label="文章内容">
                <el-input type="textarea" rows="10" v-model="form.content"></el-input>
            </el-form-item>
            <el-form-item>
                <el-button type="primary" @click="onSubmit">发布</el-button>
                <el-button @click="back">取消</el-button>
            </el-form-item>
        </el-form>

    </div>
</template>
<script>
import request from '@/utils/request'
import qs from 'querystring'
export default {
  data(){
    return {
      form:{},
      categorys:[]
    }
  },
  created(){
      //接受文章页面传递过来的文章信息
    this.form = this.$route.query;
    this.loadCategorys();
  },
  methods:{
    loadCategorys() {
      //调用ajax查询文章
      request
        .get("/category/cacadeFindAll")
        .then(result => {
          this.categorys = result.data;
        });
    },
    back(){
      this.$router.go(-1);
    },
    onSubmit(){
      // alert(JSON.stringify(this.form))
      // alert(qs.stringify(this.form));
      // 通过ajax将前端收集的数据this.form发送给服务接口
      let url = "/article/saveOrUpdate";
      request.request({
        url,
        method:"post",
        headers:{
          "Content-Type":"application/x-www-form-urlencoded"
        },
        data:qs.stringify(this.form)
      })
      .then(result=>{
        this.$message({
          message:result.message,
          type:"success"
        });
        this.back();
      })
    }
  }

}
</script>
<style scoped>

</style>
