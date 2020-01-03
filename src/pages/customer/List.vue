<template>
    <div>

      <el-button type="success" size="small" @click="toAddHandler">添加顾客</el-button>
      <el-button type="danger" size="small">删除顾客</el-button>

      <el-table :data="customers">
      <el-table-column prop="id" label="编号"></el-table-column>
      <el-table-column prop="realname" label="姓名"></el-table-column>
      <el-table-column prop="telephone" label="联系方式"></el-table-column>
      <el-table-column label="操作">
          <template v-slot="slot">
              <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
               <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
               </template>
      </el-table-column>
      </el-table>

     <el-pagination
    layout="prev, pager, next"
    :total="50">
  </el-pagination>

  <el-dialog
    title="录入顾客信息"
    :visible.sync="visible"
    width="60%">
    ---{{form }}
    <el-form :model="form" label-width="80px">
      <el-form-item label="用户名">
        <el-input v-model="form.username">
        </el-input>
      </el-form-item>
      <el-form-item label="密码">
        <el-input type="password" v-model="form.password">
        </el-input>
      </el-form-item>
      <el-form-item label="真实姓名">
        <el-input type="realname" v-model="form.realname">
        </el-input>
      </el-form-item>
      <el-form-item label="电话号码">
        <el-input type="telephone" v-model="form.telephone">
        </el-input>
      </el-form-item>
    </el-form>
   
    <span slot="footer" class="dialog-footer">
      <el-button size="small" @click="closrModalHandler">取 消</el-button>
      <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
    </span>
  </el-dialog>

    </div>   
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    //用于存放网页中调用的方法
    
    methods:{
      loadData(){
   let url = "http://134.175.154.93:6677/customer/findAll"
   request.get(url).then((response)=>{
    //  将查询结果设置到customer中
     this.customers = response.data;
   })

      },
        submitHandler(){
          //this.form对象  ----字符串---》后台
          //通过request与后台进行交互，并且携带参数
          let url = "http://134.175.154.93:6677/customer/saveOrUpdate"
           request({
             url,
             method:"POST",
             headers:{
               "Content-Type":"application/x-www-form-urlencoded"
             },
             data:querystring.stringify(this.form)
           }).then((response)=>{
             //请求结束，模态框关闭
               this.closrModalHandler();
               //刷新
               //提示消息
               this.$message({
                 type:"success",
                 message:response.message
               })
           })     
        },
        toDeleteHandler(id){
           //确认
          this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          //调用后台接口,完成删除操作
          let url = "http://134.175.154.93:6677/customer/deleteById?id="+id;
          request.get(url).then((response)=>{
            //刷新数据
            this.loadData();
            //提示结果
             this.$message({
            type: 'success',
            message: '删除成功!'+id
          });
          })
         
        })
        },
        toUpdateHandler(row){
          //模态框表单中显示当前的信息
            this.form=row;
            this.visible=true;
        },
        closrModalHandler(){
             this.visible=false;
        },
        toAddHandler(){
           this.form = {
             type:"customer"
           }
            this.visible=true
        }
    },

    //用于存放要往网页中显示的数据
    data(){
        return{
            visible:false,
          customers:[],
          form:{
            type:"customer"
          }
             }
},
created(){
  //this为当前Vue实例
  //文档vue实例创建完毕
  this.loadData();
}
}
</script>
<style scoped>


</style>