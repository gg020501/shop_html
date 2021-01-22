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

        <el-table-column prop="id" label="序号" width="134"> </el-table-column>
        <el-table-column  prop="name" label="名称" width="134"> </el-table-column>
        <el-table-column prop="title" label="标题" width="134"> </el-table-column>
        <el-table-column  prop="bandid" label="品牌" width="134" :formatter="pingpai"> </el-table-column>
        <el-table-column  prop="typeid" label="类型" width="123" > </el-table-column>

        <el-table-column  prop="imgpath" label="图片l" width="70">
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
            <el-button type="success" @click="weihuvalue(scope.row)">维护属性信息</el-button>
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


      <!-- 填写商品属性 -->
      <el-dialog title="商品属性" :visible.sync="shangpinshuxing">
        <el-form  :model="ruleForm" label-width="180px">

            <el-form-item label="属性类型" prop="typeid">
              <el-select v-model="ruleForm.typeid" placeholder="请选择活动区域" @change="selecttypebyid1(ruleForm.typeid)">
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
                <el-input v-if="a.type==3" v-model="a.sxchecks"></el-input>
                <!--  0 下拉框  -->
                <el-select v-if="a.type==0" v-model="a.sxchecks" placeholder="请选择">
                  <el-option v-for="b in a.values" :key="b.id"  :label="b.name" value="b.id"></el-option>
                </el-select>
                <!--  1 单选框  -->
                <el-radio-group v-if="a.type==1" v-model="a.sxchecks" >
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

        </el-form>

        <div slot="footer" class="dialog-footer">
          <el-button @click="shangpinshuxing = false">取 消</el-button>
          <el-button type="primary" @click="dialogFormVisible = false">确 定</el-button>
        </div>

      </el-dialog>


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
            //sku数据
            skuData:[],
            //下拉框
            ruleForm:{
              typeid:""
            },
            //品牌数据
            types:[],
            typeData:[],
            typeName:'',
            //表头
            tabletd :[],
            //table是否显示
            checkTable:false,
            //商品属性
            shangpinshuxing:false,
            //修改页面
            updFormFlag:false,
            //非sku数据
            sxData:[],
            //表格
            tableData:[],
            //品牌
            pinpaiData:[],
            //前台goods数据
            goodsData:[],
            //分页数据需要参数
            count:0,
            pageSizes:[3,6,8,10],
            params: {
              name: "",
              start: 1,
              size: 3
            },

            //修改
            saveBandForm:{
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
            },weihuvalue:function (row) {
                  this.shangpinshuxing = true;
                  this.ruleForm.typeid = row.typeid;
                  this.queryType();
                  this.selecttypebyid(row.typeid,row.id);
                  this.checkTablechange();
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
                  selecttypebyid1:function (typeid) {
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
                              sx[i].sxchecks = [];
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
                  }
                ,
                selecttypebyid:function (typeid,id) {
                  ajax.get("http://127.0.0.1:8080/api/goods/selectproductproid?id="+id).then(rs=>{
                    let datas = rs.data.data;
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
                              if(sx[i].type == 2){
                                this.getValeu(sx[i].name,datas)==""?sx[i].sxchecks=[]:sx[i].sxchecks=this.getValeu(sx[i].name,datas);
                              }else{
                                sx[i].sxchecks = this.getValeu(sx[i].name,datas);
                              }
                              sx[i].sxchecks = [];
                              //查找不是输入框的数据
                              ajax.get("http://127.0.0.1:8080/api/shuxingvalue/selectsxvalueattid?id="+sx[i].id).then(rs=>{
                                sx[i].values = rs.data;
                                sx[i].sxchecks = this.getValeu(sx[i].name,datas);
                                this.sxData.push(sx[i]);
                              })
                            }else{
                              this.sxData.push(sx[i]);
                            }
                          }else{
                            if(sx[i].type!=3){
                              if(sx[i].type==2){
                                this.getValeu(sx[i].name,datas)==""?sx[i].sxchecks=[]:sx[i].sxchecks=this.getValeu(sx[i].name,datas)
                              }else{
                                sx[i].sxchecks = this.getValeu(sx[i].name,datas);
                              }
                              //查找不是输入框的数据
                              ajax.get("http://127.0.0.1:8080/api/shuxingvalue/selectsxvalueattid?id="+sx[i].id).then(rs=>{
                                sx[i].values = rs.data;
                                sx[i].sxchecks = this.getValeu(sx[i].name,datas);
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


                  })
                },

                getValeu:function(key,data){
                  let  arrValue=[];
                  debugger;
                  //遍历当前商品对应的所有的属性
                  for (let i = 0; i <data.length ; i++) {
                    //得到当前属性数据的一个 将字符串转为json
                    let  objData=JSON.parse(data[i].attrdata);
                    // 判断当前的属性数据 是否为想要的属性值
                    if(objData[key]!=null){ //找到对应的值了
                      //判断当前属性是否为sku
                      if(objData.storcks!=null){
                        if(arrValue.indexOf(objData[key])==-1){
                          arrValue.push(objData[key]);
                        }
                      }else{
                        return objData[key];
                      }
                    }
                  }
                  return arrValue;
                },
                checkTablechange:function () {
                  this.tableData = [];
                  this.tabletd = [];
                  let dikaParams = [];
                  //console.log(this.skuData);
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
                      if(typeof datas[i] == "object"){
                        //for循环将其放入json中
                        for (let j = 0; j <datas[i].length ; j++) {
                          //获取name当表头放入json
                          let dt = this.tabletd[j].name;
                          jsons[dt] = datas[i][j];
                        }
                      }else{
                        let dt = this.tabletd[0].name;
                        jsons[dt] = datas[i];
                      }
                      //放入
                      this.tableData.push(jsons);
                    }
                  }
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

</style>
