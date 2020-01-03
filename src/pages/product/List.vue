<template>
    <div>
        <!--按钮 -->
        <div>
            <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
            <el-button type="danger" size="small">批量删除</el-button>
        </div>
        <!-- 按钮结束-->
        <!--表格-->
         <el-table :data="products">
            <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
            <el-table-column fixed="left" prop="name" label="产品名称"></el-table-column>
                <el-table-column prop="description" label="描述"></el-table-column>
            <el-table-column width="120" prop="price" label="价格"></el-table-column>
            <el-table-column  width="200" prop="status" label="所属产品"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                    <!-- {{slot.row}} -->
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                </template>
            </el-table-column>   
        </el-table>
        <!--表格结束-->
        <!--分页-->
        <el-pagination layout="prev, pager, next" :total="50">
        </el-pagination>
        <!--分页结束-->
        <!--模态框-->
        <el-dialog
            :title="title"
            :visible.sync="visible"
            width="60%">
            测试:{{form}}
            <el-form :model="form" label-width="80px">
                <el-form-item label="产品名称">
                    <el-input v-model="form.name"></el-input>
                </el-form-item>
                <el-form-item label="编号">
                    <el-input v-model="form.id"></el-input>
                </el-form-item>
                <el-form-item label="描述">
                    <el-input v-model="form.description"></el-input>
                </el-form-item>
                <el-form-item label="价格">
                    <el-input v-model="form.price"></el-input>
                </el-form-item>
                <el-form-item label="所属产品">
                    <el-input v-model="form.status"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button size="small" @click="closeModalHandler">取 消</el-button>
                <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
            </span>
        </el-dialog>
        <!--模态框结束 -->
    </div>
</template>

<script>
import request from '@/utils/request'//自定义库
import querystring from 'querystring'//系统库
export default {
    methods:{
        //提交
        submitHandler(){
            let url="http://134.175.154.93:6677/product/findAll";
            //前端向后台发送请求，完成数据的保存数据
            request({
                url,
                method:"post",
                //告诉给后台我的请求体中放的是查询字符串
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                //请求体中的数据，将this.form转换为查询字符串发送给后台
                data:querystring.stringify(this.form)
            }).then((response)=>{
                this.closeModalHandler();
                this.loadDate();
                this.$message({
                    type:"success",
                    message:response.message
                })
            })
        },
        //重载员工数据
        loadDate(){
            let url = "http://134.175.154.93:6677/product/findAll"
            request.get(url).then((response)=>{
                //箭头函数中的this指向外部函数的this
                this.products = response.data;
            })
        },
        toDeleteHandler(id){
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
        toUpdateHandler(row){
            this.title="修改产品信息",
            this.visible=true;
        },
        toAddHandler(){
            this.title="添加产品信息"
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        }
    },
 data(){
        return{
            title:"录入产品信息",
            visible:false,
            products:[],
            form:{
                type:"waiter"
            }
        }
    },
    created(){
        //在页面加载时加载数据
        this.loadDate();
    }
    
}
</script>

<style scoped>

</style>
