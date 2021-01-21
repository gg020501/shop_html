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
          <el-form-item label="名称" prop="name">
            <el-input v-model="productSaveForm.name" width="200px"></el-input>
          </el-form-item>
        </div>

        <div>
          <el-form-item label="标题" prop="title">
            <el-input v-model="productSaveForm.title" ></el-input>
          </el-form-item>
        </div>

        <div align="left">
          <el-form-item label="类型" prop="bandid">
            <el-select v-model="productSaveForm.bandid" placeholder="请选择">
              <el-option
                v-for="item in pinpaiData"
                :key="item.id"
                :label="item.name"
                :value="item.id">
              </el-option>
            </el-select>
          </el-form-item>
        </div>

        <div>
          <el-form-item label="介绍" prop="productdecs">
            <el-input v-model="productSaveForm.productdecs" type="textarea" maxlength="50" show-word-limit></el-input>
          </el-form-item>
        </div>

        <div align="left">
          <el-form-item  label="主图" prop="imgpath">
              <el-upload
                :limit="imgpathsl"
                action="http://127.0.0.1:8080/api/pinpai/insertimgpath"
                list-type="picture-card"
                :on-success="handlePictureCardPreview"
                :on-remove="handleRemove">
                <i class="el-icon-plus"></i>
              </el-upload>
              <img width="100%" :src="dialogImageUrl" >
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
      <el-form label="left" :model="ruleForm" :rules="productsaveform" v-if="active==1?active2=true:active2=false" label-width="100px">

        <div align="left">
          <el-form-item label="属性类型" prop="typeid">
            <el-select v-model="ruleForm.typeid" placeholder="请选择活动区域" @change="selecttypebyid(ruleForm.typeid)">
              <el-option
                v-for="item in types"
                :key="item.id"
                :label="item.name"
                :value="item.id">
              </el-option>
            </el-select>
          </el-form-item>

          <el-form-item v-if="skuData.length>0" label="商品规格" prop="name">
            <el-form-item v-for="a in  skuData" :key="a.id" :label="a.namech">

              <!--  3  输入框  -->
              <el-input v-if="a.type==3" v-model="input"></el-input>
              <!--  0 下拉框  -->
              <el-select v-if="a.type==0" v-model="select" placeholder="请选择">
                <el-option v-for="b in a.values" :key="b.id"  :label="b.name" value="b.id"></el-option>
              </el-select>
              <!--  1 单选框  -->
              <el-radio-group v-if="a.type==1" v-model="radioplus" >
                <el-radio-button  v-for="b in a.values" :key="b.id" :label="b.name"></el-radio-button>
              </el-radio-group>
              <!-- 2  复选框   -->
              <el-checkbox-group v-model="a.sxchecks" v-if="a.type==2"  @change="checkTablechange"  >
                <el-checkbox-button v-for="b in a.values" :key="b.id" :label="b.name" name="type"></el-checkbox-button>
              </el-checkbox-group>

            </el-form-item>
          </el-form-item>

          <div >

            <el-table
              v-if="checkTable == true"
              :data="tableData"
              style="width: 100%">
              <!--   动态展示列头  sku属性中文名 -->
              <el-table-column v-for="c in tabletd" :key="c.id" :label="c.namech" :prop="c.name">
              </el-table-column>

              <el-table-column
                label="库存"
                width="180">

                <template slot-scope="scope">
                  <el-input v-model="scope.row.storcks"/>
                </template>

              </el-table-column>
              <el-table-column
                label="价格"
                width="180">
                <template slot-scope="scope">
                  <el-input v-model="scope.row.pricess"/>
                </template>
              </el-table-column>
            </el-table>

          </div>


          <el-form-item v-if="sxData.length>0" label="商品参数" prop="name">
            <el-form-item v-for="a in  sxData" :key="a.id" :label="a.namech">

              <!--  3  输入框  -->
              <el-input v-if="a.type==3" v-model="a.sxchecks"></el-input>
              <!--  0 下拉框  -->
              <el-select v-if="a.type==0" v-model="a.sxchecks" placeholder="请选择">
                <el-option v-for="b in a.values" :key="b.id"  :label="b.name" value="b.id"></el-option>
              </el-select>
              <!--  1 单选框  -->
              <el-radio-group v-if="a.type==1" v-model="a.sxchecks" >
                <el-radio-button v-for="b in a.values" :key="b.id" :label="b.name"></el-radio-button>
              </el-radio-group>
              <!-- 2  复选框   -->
              <el-checkbox-group v-model="a.sxchecks" v-if="a.type==2">
                <el-checkbox-button v-for="b in a.values" :key="b.id" :label="b.name" name="type"></el-checkbox-button>
              </el-checkbox-group>

            </el-form-item>
          </el-form-item>

        </div>

      </el-form>
    </div>


    <div>
      <el-button style="margin-top: 12px;" v-if="active!=0" @click="nextx">上一步</el-button>
      <el-button style="margin-top: 12px;" v-if="active!=2" @click="next">下一步</el-button>
      <el-button style="margin-top: 12px;" v-if="quedinganniu==1" @click="queding">提交</el-button>
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
          //图片
          imgpathsl:1,
          dialogImageUrl: '',
          checkTable :false,
          //迪卡内容
          tableData:[],
          //table头部
          tabletd :[],
          //下拉框
          select:"",
          select1:"",
          //复选框
          check:[],
          //输入框
          input:'',
          //单选框
          radio:'',
          radioplus:'',
          //数据
          skuData:[],
          sxData:[],
          //品牌
          pinpaiData:[],
          //分类
          types:[],
          typeData:[],
          typeName:'',
          //步骤条
          quedinganniu:0,
          active: 0,
          active1:false,
          active2:false,
          active3:false,

           productSaveForm:{
            id:"",
            name:"",
            title:"",
            typeid:"",
            bandid:"",
            imgpath:"",
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
            typeid:""
          }
        };
      },created:function(){
        this.active = 0;
        this.queryType();
        this.queryPinpai();
      },methods: {
          //图片
        handleRemove(file, fileList) {
          console.log(file,fileList);
        },
        handlePictureCardPreview(file) {
            this.dialogImageUrl = file.url;
            this.productSaveForm.imgpath = file.data;
        },
        //监听下一页和上一页
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
        },queding:function () {
          this.productSaveForm.typeid = this.ruleForm.typeid;
          let attrs = [];
          for (let i = 0; i <this.sxData.length ; i++) {
            let sxData = {};
            sxData[this.sxData[i].name] = this.sxData[i].sxchecks;
            attrs.push(sxData);
          }
          this.productSaveForm.attrs = JSON.stringify(attrs);
          this.productSaveForm.sku = JSON.stringify(this.tableData);
          ajax.post("http://127.0.0.1:8080/api/goods/insertattrssku",qs.stringify(this.productSaveForm)).then(rs=>{
            if(rs.data.code == 200){
              this.$message({
                type: 'success',
                message: '新增成功!'
              });
            }
          })
          console.log(this.tableData);
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
        },queryPinpai:function () {
          var pinpaiData = this;
          ajax.post("http://127.0.0.1:8080/api/pinpai/selectpinpaiAll?start="+1+"&size="+100).then(rs=>{
            pinpaiData.pinpaiData = rs.data.data;
          });
        },selecttypebyid:function (typeid) {
            //每次选择时触发 将table设置为false隐藏
            this.checkTable = false;

            this.skuData = [];
            this.sxData = [];
            ajax.get("http://127.0.0.1:8080/api/shuxing/selecttypebyid?typeid="+typeid).then(rs=>{
              let sx = rs.data;
              if(sx.length > 0 ){
                for (let i = 0; i <sx.length ; i++) {
                  //判断是否为sku属性
                  if(sx[i].issku == 0 ){
                    if(sx[i].type!=3){
                      //查找不是输入框的数据
                      ajax.get("http://127.0.0.1:8080/api/shuxingvalue/selectsxvalueattid?id="+sx[i].id).then(rs=>{
                        sx[i].values = rs.data;
                        this.sxData.push(sx[i]);
                      })
                    }else{
                      this.sxData.push(sx[i]);
                    }
                  }else{
                    if(sx[i].type!=3){
                      //查找不是输入框的数据
                      ajax.get("http://127.0.0.1:8080/api/shuxingvalue/selectsxvalueattid?id="+sx[i].id).then(rs=>{
                        sx[i].values = rs.data;
                        sx[i].sxchecks = [];
                        this.skuData.push(sx[i]);
                      })
                    }else{
                      sx[i].sxchecks = [];
                      this.skuData.push(sx[i]);
                    }
                  }
                }
              }else{
                this.skuData = [];
                this.sxData = [];
              }
            })
          },checkTablechange:function () {

            this.tableData = [];
            this.tabletd = [];
            let dikaParams = [];

            console.log(this.skuData);
            let flag = true;
            for (let i = 0; i <this.skuData.length ; i++) {
                  this.tabletd.push({"id":this.skuData[i].id,"namech":this.skuData[i].namech,"name":this.skuData[i].name});
                  dikaParams.push(this.skuData[i].sxchecks);
                  if(this.skuData[i].sxchecks.length == 0){
                    flag = false;
                    break;
              }
            }

            if( flag == true ){
              this.quedinganniu = 1;
              //经过迪卡耳机加载的数据
              let datas = this.calcDescartes(dikaParams);
              for (let i = 0; i <datas.length ; i++) {
                //声明json串
                let jsons = {};
                //for循环将其放入json中
                for (let j = 0; j <datas[i].length ; j++) {
                  //获取name当表头放入json
                  let dt = this.tabletd[j].name;
                  jsons[dt] = datas[i][j];
                }
                //放入
                this.tableData.push(jsons);
              }
            }
            console.log(this.tabletd);
            this.checkTable = flag;

          },calcDescartes:function(array) {
          if (array.length < 2) return array[0] || [];
          return [].reduce.call(array, function (col, set) {
            var res = [];
            col.forEach(function (c) {
              set.forEach(function (s) {
                var t = [].concat(Array.isArray(c) ? c : [c]);
                t.push(s);
                res.push(t);
              })
            });
            return res;
          });
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
    color: #00ffff;
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
