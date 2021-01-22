<template>
  <div>
    <h1>品牌管理</h1>
    <div>
      <el-form :inline="true"  class="demo-form-inline">

        <el-form-item label="名称">
          <el-input v-model="name" placeholder="名称"></el-input>
        </el-form-item>

        <el-tooltip class="item" effect="dark" content="查询" placement="top-start">
          <el-button type="primary" @click="selectpinpaiAll()">查询</el-button>
        </el-tooltip>
        <el-button type="info" @click="add()">+新增</el-button>
      </el-form>

    </div>

    <div>
      <el-table :data="pinpaiData"  style="width: 100%">

        <el-table-column prop="id" label="序号" width="160"> </el-table-column>

        <el-table-column  prop="name" label="名称" width="160"> </el-table-column>

        <el-table-column  prop="bande" label="首字母" width="160"> </el-table-column>

        <el-table-column  prop="imgpath" label="图片log" width="160">
          <template slot-scope="scope" >
            <img width="50px" :src="scope.row.imgpath"/>
          </template>
        </el-table-column>

        <el-table-column  prop="banddesc" label="品牌介绍" width="160"> </el-table-column>

        <el-table-column  prop="createdate" label="创建时间" width="160"> </el-table-column>

        <el-table-column prop="updatedate" label="修改时间" width="158" > </el-table-column>

        <el-table-column prop="author" label="操作人" width="150" > </el-table-column>

        <el-table-column align="center" label="操作" width="150">
          <el-button-group slot-scope="scope">
              <el-button type="primary" icon="el-icon-edit" @click="showupdate(scope.row)"></el-button>
              <el-button type="primary" icon="el-icon-delete" @click="del(scope.row)"></el-button>
          </el-button-group>
        </el-table-column>

      </el-table>
      <font style="color: #8c939d">总数据:{{count}}条</font>
      <font v-for="page in totalPage">
        <el-button size="mini" @click="selectpinpaiAll(page)">{{page}}</el-button>
      </font>
    </div>

    <el-dialog title="录入品牌信息" :visible.sync="addFormFlag">

      <el-form :model="saveBandForm" ref="addCarForm" label-width="80px">

        <el-form-item label="名称" prop="name">
          <el-input v-model="saveBandForm.name" autocomplete="off" ></el-input>
        </el-form-item>

        <el-form-item label="首字母" prop="bande">
          <el-input v-model="saveBandForm.bande" autocomplete="off" ></el-input>
        </el-form-item>

        <el-form-item label="图片">
          <el-upload
            class="upload-demo"
            action="http://127.0.0.1:8080/api/pinpai/insertimgpath"
            :on-success="imgCallBack"
            name="file"
            list-type="picture">
            <el-button size="small" type="primary">点击上传</el-button>
          </el-upload>
        </el-form-item>

        <el-form-item label="品牌介绍" prop="banddesc">
          <el-input type="textarea" v-model="saveBandForm.banddesc" maxlength="200" show-word-limit ></el-input>
        </el-form-item>

        <el-form-item label="排序" prop="ord">
          <el-input-number v-model="saveBandForm.ord"  :min="1" :max="9999" label="描述文字"></el-input-number>
        </el-form-item>

        <el-form-item label="操作人" prop="author">
          <el-input v-model="saveBandForm.author" autocomplete="off" ></el-input>
        </el-form-item>

      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="addFormFlag = false">取 消</el-button>
        <el-button type="primary" @click="addForm">确 定</el-button>
      </div>
    </el-dialog>

    <el-dialog title="修改品牌信息" :visible.sync="updFormFlag">

      <el-form :model="updBandForm" ref="addCarForm" label-width="80px">

        <el-form-item label="名称" prop="name">
          <el-input v-model="updBandForm.name" autocomplete="off" ></el-input>
        </el-form-item>

        <el-form-item label="首字母" prop="bande">
          <el-input v-model="updBandForm.bande" autocomplete="off" ></el-input>
        </el-form-item>

        <el-form-item label="图片">
          <el-upload
            class="upload-demo"
            action="http://127.0.0.1:8080/api/pinpai/insertimgpath"
            :on-success="imgCallBack2"
            name="file"
            list-type="picture">
            <img width="50px"  :src="updBandForm.imgpath" />
            <el-button size="small" type="primary">点击上传</el-button>
          </el-upload>
        </el-form-item>

        <el-form-item label="品牌介绍" prop="banddesc">
          <el-input type="textarea" v-model="updBandForm.banddesc" maxlength="200" show-word-limit ></el-input>
        </el-form-item>

        <el-form-item label="排序" prop="ord">
          <el-input-number v-model="updBandForm.ord"  :min="1" :max="9999" label="描述文字"></el-input-number>
        </el-form-item>

        <el-form-item label="操作人" prop="author">
          <el-input v-model="updBandForm.author" autocomplete="off" ></el-input>
        </el-form-item>

      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="updFormFlag = false">取 消</el-button>
        <el-button type="primary" @click="updForm">确 定</el-button>
      </div>
    </el-dialog>



  </div>
</template>

<script>
  import qs from 'qs';
  import ajax from 'axios';
  export default {
      name: "Pinpai",
      data(){
        return{
          saveBandForm:{
            name:"",
            bande:"",
            banddesc:"",
            ord:"",
            author:"",
            imgpath:""
          }, updBandForm:{
            id:"",
            name:"",
            bande:"",
            banddesc:"",
            ord:"",
            author:"",
            imgpath:""
          },
          addFormFlag:false,
          updFormFlag:false,
          name:"",
          pinpaiData:[],
          count:0,
          totalPage:0,
          start:1,
          size:3,
          page:0,
        }
      },created:function () {
      this.selectpinpaiAll();
      },methods:{
          selectpinpaiAll:function(page){
            var data = {name:this.name,page:page};
            var start = this.start;
            var size = this.size;
            var pinpaiData = this;
            ajax.post("http://127.0.0.1:8080/api/pinpai/selectpinpaiAll?start="+start+"&size="+size,qs.stringify(data)).then(rs=>{
              pinpaiData.pinpaiData = rs.data.data;
              pinpaiData.count = rs.data.count;
              pinpaiData.totalPage = Math.ceil(pinpaiData.count/size);
            });
          },del:function (pinpai) {
              ajax.post("http://127.0.0.1:8080/api/pinpai/delectpinpaiById?id="+pinpai.id).then(rs=>{
                if(rs.data.code == 200){
                  this.$message({
                    type: 'success',
                    message: '删除成功!'
                  });
                  this.selectpinpaiAll();
                }
              });
          },add:function () {
              this.addFormFlag = true;
          },imgCallBack:function (response, file) {
            debugger
              this.saveBandForm.imgpath=response.data;
          },imgCallBack2:function (response, file) {
              this.updBandForm.imgpath=response.data;
          },addForm:function () {
              var stringify = qs.stringify(this.saveBandForm);
              ajax.post("http://127.0.0.1:8080/api/pinpai/selectpinpaiInsert",stringify).then(rs=>{
                if(rs.data.code == 200){
                  this.$message({
                    type: 'success',
                    message: '新增成功!'
                  });
                  this.selectpinpaiAll();
                }
                this.addFormFlag = false;
              })
          },showupdate:function (pinpai) {
                this.updFormFlag = true;
                this.updBandForm.id = pinpai.id;
                this.updBandForm.name = pinpai.name;
                this.updBandForm.bande = pinpai.bande;
                this.updBandForm.banddesc = pinpai.banddesc;
                this.updBandForm.ord = pinpai.ord;
                this.updBandForm.author = pinpai.author;
                this.updBandForm.imgpath = pinpai.imgpath;
          },updForm:function () {
              var stringify = qs.stringify(this.updBandForm);
                ajax.post("http://127.0.0.1:8080/api/pinpai/selectpinpaiInsert",stringify).then(rs=>{
                  if(rs.data.code == 200){
                    this.$message({
                      type: 'success',
                      message: '修改成功!'
                    });
                    this.selectpinpaiAll();
                  }
                  this.updFormFlag = false;
                })
          }
      }
  }
</script>

<style scoped>

</style>
