<template>
  <center>
  <div>
      <div>
        <el-steps :active="active" finish-status="success" simple style="margin-top: 20px">
          <el-step title="填写商品信息" ></el-step>
          <el-step title="填写商品属性" ></el-step>
          <el-step title="选择商品关联" ></el-step>
        </el-steps>
      </div>

    <!--  填写商品信息 1 -->
    <div class="container" style="width:800px" :visible.sync="active1">
      <el-form label="left" :model="productSaveForm" :rules="productsaveform" ref="productsaveform" v-if="active==0?active1=true:active1=false"  label-width="100px">

        <div>
          <el-form-item label="商品名称" prop="name">
            <el-input v-model="productSaveForm.name" width="200px"></el-input>
          </el-form-item>
        </div>

        <div>
          <el-form-item label="标题" prop="title">
            <el-input v-model="productSaveForm.title" ></el-input>
          </el-form-item>
        </div>

        <div align="left">
          <el-form-item label="商品类型" prop="bandid">
            <el-select v-model="productSaveForm.bandid" placeholder="请选择">
              <el-option
                v-for="item in types"
                :key="item.id"
                :label="item.name"
                :value="item.id">
              </el-option>
            </el-select>
          </el-form-item>
        </div>

        <div>
          <el-form-item label="商品介绍" prop="productdecs">
            <el-input v-model="productSaveForm.productdecs" type="textarea" maxlength="50" show-word-limit></el-input>
          </el-form-item>
        </div>

        <div>
          <el-form-item label="价格" prop="price">
            <el-input v-model="productSaveForm.price" :min="1" type="number" ></el-input>
          </el-form-item>
        </div>

        <div>
          <el-form-item label="库存" prop="stocks">
            <el-input v-model="productSaveForm.stocks" :min="1" type="number" ></el-input>
          </el-form-item>
        </div>

        <div>
          <el-form-item label="排序" prop="sortnum">
            <el-input v-model="productSaveForm.sortnum" :min="1" type="number" ></el-input>
          </el-form-item>
        </div>

      </el-form>
    </div>

    <!-- 填写商品促销 2 -->
    <div class="container" style="width:800px" :visible.sync="active2">
      <el-form label="left" :model="ruleForm" :rules="productsaveform" v-if="active==1?active2=true:active2=false" >

        <div align="left">
          <el-form-item label="属性类型" prop="region">
            <el-select v-model="ruleForm.id" placeholder="请选择活动区域" @change="selecttypes(ruleForm.id)">
              <el-option
                v-for="item in types"
                :key="item.id"
                :label="item.name"
                :value="item.id">
              </el-option>
            </el-select>
          </el-form-item>
        </div>

      </el-form>
    </div>










    <div>
      <el-button style="margin-top: 12px;" v-if="active!=0" @click="nextx">上一步</el-button>
      <el-button style="margin-top: 12px;" v-if="active!=2" @click="next">下一步</el-button>
      <el-button style="margin-top: 12px;" v-if="active==2" @click="submitnext">提交</el-button>
    </div>

    </div>
  </center>
</template>

<script>
  import ajax from 'axios';
  import qs from 'qs';
    export default {
        name: "Goods",
      data() {
        return {
          types:[],
          typeData:[],
          typeName:'',
          active: 0,
          active1:false,
          active2:false,
          active3:false,
          updBandForm:{

          },productSaveForm:{
            id:"",
            name:"",
            title:"",
            bandid:"",
            productdecs:"",
            price:0,
            stocks:0,
            sortnum:0
          },productsaveform:{
            name:[
              { required: true, message: '请输入商品名称', trigger: 'blur' },
              { min: 3, max: 7, message: '长度在 3 到 7 个字符', trigger: 'blur' }
            ],title:[
              { required: true, message: '请输入标题', trigger: 'blur' },
              { min:5, message: '长度至少5个字符', trigger: 'blur' }
            ],bandid:[
              { required: true, message: '请选择商品类型', trigger: 'change' }
            ],productdecs:[
              { required: true, message: '请输入商品介绍', trigger: 'blur' },
              { min: 10, max: 50, message: '长度在 10 到 50 个字符', trigger: 'blur' }
            ],
          },
          ruleForm:{
            id:""
          }


        };
      },created:function(){
        this.queryType();  
      },methods: {
        next:function() {
          this.$refs['productsaveform'].validate((vd)=>{
            if(vd == true){
              if (this.active++ > 3) this.active = 0;
            }
          })
        },nextx:function(){
          if (this.active-- < 2) this.active = 0;
        },submitnext:function () {
          alert("新增成功");
          this.active = 0;
        },queryType:function () {
          ajax.get("http://127.0.0.1:8080/api/stype/selectstypeAll").then(rs=>{
            this.typeData = rs.data.data;
            //先找到子节点的数据   this.types;
            this.getChildrenType();
            for (let i = 0; i <this.types.length ; i++) {
              this.typeName = "";
              this.chandleName(this.types[i]);
              console.log(this.types);
              var typeName1 = this.typeName.split("/").reverse().join("/");
              this.types[i].name= typeName1.substr(0,typeName1.length-1);
            }
          });
        },chandleName:function (node) {
          if(node.pid != 0){
            this.typeName+="/"+node.name;
            for (let i = 0; i <this.typeData.length ; i++) {
              if(node.pid == this.typeData[i].id){
                this.chandleName(this.typeData[i]);
                break;
              }
            }
          }else{
            this.typeName+="/"+node.name;
          }
        },getChildrenType:function () {
          for (let i = 0; i <this.typeData.length ; i++) {
            let  node=this.typeData[i];
            this.isChildrenNode(node);
          }
        },isChildrenNode:function (node) {
          let rs = true;
          for (let i = 0; i <this.typeData.length ; i++) {
            if(node.id == this.typeData[i].pid ){
              rs = false;
              break;
            }
          }
          if(rs==true){
            this.types.push(node);
          }
        },



          selecttypes:function (id) {
            alert(id);
          }


      }
    }
</script>

<style scoped>
  .handle-box {
    margin-bottom: 20px;
  }

  .handle-select {
    width: 120px;
  }

  .handle-input {
    width: 300px;
    display: inline-block;
  }
  .table {
    width: 100%;
    font-size: 14px;
  }
  .red {
    color: #ff0000;
  }
  .mr10 {
    margin-right: 10px;
  }
  .table-td-thumb {
    display: block;
    margin: auto;
    width: 40px;
    height: 40px;
  }
</style>
