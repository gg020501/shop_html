<template>
  <center>


    <div>
        <h1>商品管理</h1>

      <el-form :inline="true"  class="demo-form-inline">

        <el-form-item label="名称">
          <el-input v-model="params.name" placeholder="名称"></el-input>
        </el-form-item>

        <el-tooltip class="item" effect="dark" content="查询" placement="top-start">
          <el-button type="primary" @click="selectgoodsj">查询</el-button>
        </el-tooltip>
      </el-form>


      <el-table :data="goodsData"  style="width: 100%">

        <el-table-column prop="id" label="序号" width="150"> </el-table-column>
        <el-table-column  prop="name" label="名称" width="150"> </el-table-column>
        <el-table-column prop="title" label="标题" width="150"> </el-table-column>
        <el-table-column  prop="bandid" label="品牌" width="150" :formatter="pingpai"> </el-table-column>

        <el-table-column  prop="imgpath" label="图片l" width="150">
          <template slot-scope="scope" >
            <img width="50px" :src="scope.row.imgpath"/>
          </template>
        </el-table-column>

        <el-table-column  prop="productdecs" label="商品介绍" width="150"> </el-table-column>
        <el-table-column  prop="price" label="价格" width="150"> </el-table-column>
        <el-table-column prop="stocks" label="库存" width="150" > </el-table-column>

        <el-table-column
          prop="id"
          label="操作">
          <template slot-scope="scope">
            <el-button type="primary" icon="el-icon-edit"   @click="showupdate(scope.row)"></el-button>
            <el-button type="primary" icon="el-icon-delete"   @click="deleterow(scope.row.id)"></el-button>
            <el-button type="success" @click="weihuvalue(scope.row)">维护信息</el-button>
          </template>
        </el-table-column>

      </el-table>

      <el-dialog title="编辑商品信息" :visible.sync="updFormFlag">

        <el-form :model="saveBandForm" ref="addCarForm" label-width="80px">

          <el-form-item label="名称" prop="name">
            <el-input v-model="saveBandForm.name" autocomplete="off" ></el-input>
          </el-form-item>

          <el-form-item label="标题" prop="title">
            <el-input v-model="saveBandForm.title" autocomplete="off" ></el-input>
          </el-form-item>

          <el-form-item label="品牌" prop="bandid">
            <el-select v-model="saveBandForm.bandid" placeholder="请选择">
              <el-option v-for="item in pinpaiData" :key="item.id" :label="item.name" :value="item.id">
              </el-option>
            </el-select>
          </el-form-item>

          <el-form-item label="图片">
            <el-upload
              class="upload-demo"
              action="http://127.0.0.1:8080/api/pinpai/insertimgpath"
              :on-success="imgCallBack2"
              name="file"
              list-type="picture">
              <img width="50px"  :src="saveBandForm.imgpath" />
              <el-button size="small" type="primary">点击上传</el-button>
            </el-upload>
          </el-form-item>

          <el-form-item label="商品介绍" prop="productdecs">
            <el-input type="textarea" v-model="saveBandForm.productdecs" maxlength="200" show-word-limit ></el-input>
          </el-form-item>

          <el-form-item label="价格" prop="price">
            <el-input v-model="saveBandForm.price" autocomplete="off" ></el-input>
          </el-form-item>

          <el-form-item label="库存" prop="stocks">
            <el-input v-model="saveBandForm.stocks" autocomplete="off" ></el-input>
          </el-form-item>

        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="saveBandForm = false">取 消</el-button>
          <el-button type="primary" @click="updFormgoods">确 定</el-button>
        </div>
      </el-dialog>




      <!--分页插件-->
      <div class="block" align="center">
        <el-pagination
          layout="sizes,prev,pager,next,jumper"
          :total="count"
          background
          :page-size="params.size"
          :page-sizes="pageSizes"
          @size-change="sizeChange"
          @current-change="pageChange"
        >
        </el-pagination>
      </div>


    </div>
  </center>
</template>

<script>
  import ajax from 'axios';
  import qs from 'qs';
    export default {
        name: "Goodssj",
        data(){
          return {
            updFormFlag:false,
            pinpaiData:[],
            goodsData:[],
            count:0,
            pageSizes:[3,6,8,10],
            params: {
              name: "",
              start: 1,
              size: 3
            },saveBandForm:{
              id:"",
              name:"",
              title:"",
              imgpath:"",
              bandid:"",
              productdecs:"",
              price:"",
              stocks:"",

            }
          }
        },created:function () {
            this.selectgoodsj();
            this.selectpinpai();
        },methods:{
          sizeChange:function (size) {
            this.params.size = size;
            this.selectgoodsj();
          },pageChange:function (start) {
            this.params.start = start;
            this.selectgoodsj();
          },selectgoodsj:function () {
            ajax.post("http://127.0.0.1:8080/api/goods/selectgoodsj",qs.stringify(this.params)).then(rs=>{
              console.log(this.params);
              debugger
              this.goodsData = rs.data.data;
              this.count = rs.data.count;
            });
          },showupdate:function (row) {
              this.saveBandForm = row;
              this.updFormFlag = true;
            },deleterow:function (id) {
                ajax.get("http://127.0.0.1:8080/api/goods/deletegoodsbyid?id="+id).then(rs=>{
                  if(rs.data.code == 200){
                    this.$message({
                      type: 'success',
                      message: '删除成功!'
                    });
                    this.selectgoodsj();
                  }
                })
            },pingpai:function (a,b,c,d) {
              for (let i = 0; i <this.pinpaiData.length ; i++) {
                if(c == this.pinpaiData[i].id){
                    return this.pinpaiData[i].name;
                }
              }
              return "";
            },selectpinpai:function () {
              ajax.post("http://127.0.0.1:8080/api/pinpai/selectpinpaiAll?start=1&size=1000").then(rs=>{
                 this.pinpaiData = rs.data.data;
              });
            },imgCallBack2:function (response, file) {
              this.saveBandForm.imgpath=response.data;
            },updFormgoods:function () {
                ajax.post("http://127.0.0.1:8080/api/goods/insertgoods",qs.stringify(this.saveBandForm)).then(rs=>{
                  if(rs.data.code == 200){
                    this.$message({
                      type: 'success',
                      message: '修改成功!'
                    });
                    this.updFormFlag = false;
                    this.selectgoodsj();
                  }
                })
            }




        }















    }
</script>

<style scoped>

</style>
