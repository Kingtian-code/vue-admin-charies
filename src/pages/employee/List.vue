<template>
    <div>
        <!--按钮-->
        <el-button size="small" type="primary" @click="toAddHandler">添加员工</el-button>
        <el-button size="small" type="danger">删除员工</el-button>

        <!--表格-->
        <el-table :data="employees">
            <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
            <el-table-column fixed="left" prop="realname" label="姓名"></el-table-column>
            <el-table-column prop="gender" label="性别"></el-table-column>
            <el-table-column width="120" prop="telephone" label="手机号"></el-table-column>
            <el-table-column width="200" prop="bankCard" label="银行卡号"></el-table-column>
            <el-table-column width="200" prop="idCard" label="身份证号"></el-table-column>
            <el-table-column fixed="right" label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler">修改</a>
                </template>
            </el-table-column>
        </el-table>
        <!--表格结束-->

        <!--分页开始-->
        <el-pagination
            small
            layout="prev, pager, next"
            :total="50">
        </el-pagination>
        <!--分页结束-->

        <el-dialog
            :title="title"
            :visible.sync="visible"
            width="60%">
            测试：{{form}}
            <el-form :model="form" label-width="80px">
                <el-form-item label="用户名">
                    <el-input v-model="form.username"></el-input>
                </el-form-item>
                <el-form-item label="密码">
                    <el-input v-model="form.password"></el-input>
                </el-form-item>
                <el-form-item label="姓名">
                    <el-input v-model="form.realname"></el-input>
                </el-form-item>
                <el-form-item label="性别">
                    <el-radio-group v-model="form.gender">
                        <el-radio label="男">男</el-radio>
                        <el-radio label="女">女</el-radio>
                    </el-radio-group>
                </el-form-item>
                <el-form-item label="手机号">
                    <el-input v-model="form.telephone"></el-input>
                </el-form-item>
                <el-form-item label="身份证号">
                    <el-input v-model="form.idCard"></el-input>
                </el-form-item>
                <el-form-item label="银行卡号">
                    <el-input v-model="form.bankCard"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button size="small" @click="closeModalHandler">取 消</el-button>
                <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'    //导入一个系统库，用来查询字符串
export default {
     methods:{
         submitHandler(){
             //前端向后台发送请求，完成数据的保存操作
             let url="http://134.175.154.93:6677/waiter/saveOrUpdate"
             request({
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                 //将this.form转换为查询字符串发送给后台
                data:querystring.stringify(this.form)
             }).then((response)=>{
                //请求结束，模态框关闭
                this.closeModalHandler();
                this.$message({
                    type:"success",
                    message:response.message
                })
                this.loadData();
            })
         },
         //重载员工数据
         loadData(){
             //this->VUE实例，通过VUE实例访问该实例中数据
             let url="http://134.175.154.93:6677/waiter/findAll"
             request.get(url).then((response)=>{
                //而箭头函数中的this指向外部函数中的this
                this.employees=response.data;
             })
         },
        toDeleteHandler(id){
            //先确认
          this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$message({
            type: 'success',
            message: '删除成功!'+id
          });
        })
        },
        toUpdateHandler(){
            this.title="修改员工信息"
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },
        toAddHandler(){
            this.visible=true;
        }
    },
    //生命周期,VUE实例完毕后要执行的操作
    created(){
       this.loadData();
    },
    //用于存放要向网页中显示的数据
    data(){
        return{
            title:"录入员工信息",
            visible:false,
            employees:[],
            form:{
                type:"waiter"
            }
        }
    }
}
</script>

<style scoped>

</style>
