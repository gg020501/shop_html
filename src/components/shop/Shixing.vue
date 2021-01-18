<template>
  <div>


    <!--属性value-->
    <el-dialog :title="shuxingtitle" :visible.sync="sxFormFlag">
      <el-button type="success" @click="toaddvalue">新增</el-button>
      <el-table
        :data="sxvalueData"
        style="width: 100%">

        <el-table-column
          prop="id"
          label="序号"
          width="180">
        </el-table-column>

        <el-table-column
          prop="name"
          label="名称"
          width="180">
        </el-table-column>

        <el-table-column
          prop="namech"
          label="英文名">
        </el-table-column>

        <el-table-column
          prop="attid"
          label="属性">
        </el-table-column>

        <el-table-column
          prop="id"
          label="操作">
          <template slot-scope="scope">
            <el-button type="primary" icon="el-icon-edit" @click="toupdatevalue(scope.row)"></el-button>
            <el-button type="danger" icon="el-icon-delete" @click="delvalueId(scope.row.id)"></el-button>
          </template>
        </el-table-column>
      </el-table>

    </el-dialog>

    <el-dialog title="新增属性" :visible.sync="addShuXing">

      <el-form :model="addSx" :rules="addFormsx" ref="addformshuxing" label-width="100px">

        <el-form-item label="属性英文名" prop="name">
          <el-input v-model="addSx.name" autocomplete="off" ></el-input>
        </el-form-item>

        <el-form-item label="属性中文名" prop="namech">
          <el-input v-model="addSx.namech" autocomplete="off" ></el-input>
        </el-form-item>

      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="submitSx">提 交</el-button>
        <el-button @click="addShuXing = false">取 消</el-button>
      </div>
    </el-dialog>

    <el-dialog title="修改属性" :visible.sync="updShuXing">

      <el-form :model="updSx" :rules="addFormsx" ref="addformshuxing" label-width="100px">

        <el-form-item label="属性英文名" prop="name">
          <el-input v-model="updSx.name" autocomplete="off" ></el-input>
        </el-form-item>

        <el-form-item label="属性中文名" prop="namech">
          <el-input v-model="updSx.namech" autocomplete="off" ></el-input>
        </el-form-item>

      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="updateSx">提 交</el-button>
        <el-button @click="updShuXing = false">取 消</el-button>
      </div>
    </el-dialog>



    <el-form :inline="true"  class="demo-form-inline">
      <el-form-item label="名称">
        <el-input v-model="name" placeholder="名称"></el-input>
      </el-form-item>
      <el-tooltip class="item" effect="dark" content="查询" placement="top-start">
        <el-button type="primary" @click="selectshuxingAll()">查询</el-button>
      </el-tooltip>
      <el-button type="success" @click="toadd">新增</el-button>
    </el-form>

    <el-table
      :data="ShuxingData"
      style="width: 100%">

      <el-table-column
        prop="id"
        label="序号"
        width="180">
      </el-table-column>

      <el-table-column
        prop="name"
        label="名称"
        width="180">
      </el-table-column>

      <el-table-column
        prop="namech"
        label="英文名">
      </el-table-column>

      <el-table-column
        prop="typeid"
        label="商品类型"
        :formatter="ftype">
      </el-table-column>

      <el-table-column
        prop="type"
        label="属性类型"
        :formatter="fmt">
      </el-table-column>

      <el-table-column
        prop="id"
        label="操作">
        <template slot-scope="scope">
          <el-button type="primary" icon="el-icon-edit"   @click="toupdate(scope.row)"></el-button>
          <el-button type="danger" icon="el-icon-delete"  @click="del(scope.row.id)"></el-button>
          <el-button type="success" v-if="scope.row.type != 3" @click="weihuvalue(scope.row)">维护信息</el-button>
        </template>
      </el-table-column>
    </el-table>

    <font style="color: #8c939d">总数据:{{count}}条</font>
    <font v-for="page in totalPage">
      <el-button size="mini" @click="selectshuxingAll(page)">{{page}}</el-button>
    </font>

    <!--新增模板-->
    <el-dialog title="品牌信息" :visible.sync="addFormFlag">

      <el-form :model="addForm" :rules="addrules" ref="valueform" label-width="100px">

        <el-form-item label="属性中文名" prop="name">
          <el-input v-model="addForm.name" autocomplete="off" ></el-input>
        </el-form-item>

        <el-form-item label="属性英文名" prop="namech">
          <el-input v-model="addForm.namech" autocomplete="off" ></el-input>
        </el-form-item>

       <el-form-item label="商品类型" prop="typeid">
          <el-select v-model="addForm.typeid" placeholder="请选择">
            <el-option
              v-for="item in types"
              :key="item.id"
              :label="item.name"
              :value="item.id">
            </el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="属性类型" prop="type">
          <el-radio v-model="addForm.type" :label="0">下拉框</el-radio>
          <el-radio v-model="addForm.type" :label="1">单选框</el-radio>
          <el-radio v-model="addForm.type" :label="2">复选框</el-radio>
          <el-radio v-model="addForm.type" :label="3">输入框</el-radio>
        </el-form-item>

        <el-form-item label="是否为SKU" prop="issku">
          <el-radio v-model="addForm.issku" :label="0">是</el-radio>
          <el-radio v-model="addForm.issku" :label="1">否</el-radio>
        </el-form-item>

      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="addFormFlag = false">取 消</el-button>
        <el-button type="primary" @click="addShopData">确 定</el-button>
      </div>
    </el-dialog>


    <!--修改模板-->
    <el-dialog title="修改品牌信息" :visible.sync="updFormFlag">

      <el-form :model="updForm" ref="addForm" label-width="100px">

        <el-form-item label="属性英文名" prop="name">
          <el-input v-model="updForm.name" autocomplete="off" ></el-input>
        </el-form-item>

        <el-form-item label="属性中文名" prop="nameCH">
          <el-input v-model="updForm.namech" autocomplete="off" ></el-input>
        </el-form-item>

        <el-form-item label="商品类型" prop="typeId">
          <el-select v-model="updForm.typeid" placeholder="请选择">
            <el-option v-for="item in types" :key="item.id" :label="item.name" :value="item.id">
            </el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="属性类型" prop="type">
          <el-radio v-model="updForm.type" :label="0">下拉框</el-radio>
          <el-radio v-model="updForm.type" :label="1">单选框</el-radio>
          <el-radio v-model="updForm.type" :label="2">复选框</el-radio>
          <el-radio v-model="updForm.type" :label="3">输入框</el-radio>
        </el-form-item>

        <el-form-item label="是否为SKU" prop="isSKU">
          <el-radio v-model="updForm.issku" :label="0">是</el-radio>
          <el-radio v-model="updForm.issku" :label="1">否</el-radio>
        </el-form-item>

      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="updFormFlag = false">取 消</el-button>
        <el-button type="primary" @click="updShopData">确 定</el-button>
      </div>
    </el-dialog>

  </div>
</template>

<script>
  import ajax from 'axios';
  import qs from 'qs';
    export default {
        name: "Shixing",
      data(){
        var checkname = (rule, value, callback) => {
          if (!value) {
            return callback(new Error('属性名不能为空'));
          }
          if(/^[\u4e00-\u9fa5]+$/i.test(value)){
            callback();
          }else{
            callback(new Error('只能输入中文'));
          }
        };
        var checknamech = (rule, value, callback) => {
          if (!value) {
            return callback(new Error('属性名不能为空'));
          }
          if(/^[A-Za-z]+$/i.test(value)){
            callback();
          }else{
            callback(new Error('只能输入英文'));
          }
        };
          return{
            shuxingtitle:"",
            attid:'',
            name:'',
            sxvalueData:[],
            //digui
            types:[],
            typeData:[],
            typeName:'',
            //digui
            ShuxingData:[],
            count:0,
            start:1,
            size:3,
            page:0,
            totalPage:0,
            updFormFlag:false,
            addFormFlag:false,
            sxFormFlag:false,
            addShuXing:false,
            updShuXing:false,
            updForm:{
              id:"",
              name:"",
              namech:"",
              typeid:"",
              type:"",
              issku:""
            },addForm:{
              name:"",
              namech:"",
              typeid:"",
              type:"",
              issku:""
            },addrules:{
              name:[
                { required: true, message: '请输入属性英文名', trigger: 'blur' },
                { min: 3, max: 7, message: '长度在 3 到 7 个字符', trigger: 'blur' }
              ],
              namech:[
                { required: true, message: '请输入属性中文名', trigger: 'blur' },
                { min: 3, max: 7, message: '长度在 3 到 7 个字符', trigger: 'blur' }
              ],
              typeid:[
                { required: true, message: '请选择商品类型', trigger: 'change' }
              ],
              type:[
                { required: true, message: '请选择属性类型', trigger: 'change' }
              ],
              issku:[
                { required: true, message: '请选择是否为sku', trigger: 'change' }
              ]

            },addSx:{
              name:"",
              namech:"",
              attid:""
            },addFormsx:{
              name:[
                { required: true, message: '请输入属性值的名称', trigger: 'blur' },
                { max: 10, message: '长度不能超过 10 个字符', trigger: 'blur' },
                { validator:checknamech,trigger: 'blur' }
              ],
              namech:[
                { required: true, message: '请输入属性值的名称', trigger: 'blur' },
                { max: 10, message: '长度不能超过 10 个字符', trigger: 'blur' },
                { validator:checkname,trigger: 'blur' }
              ]
            },updSx:{
              attid:"",
              id:"",
              isdel:"",
              name: "",
              namech: ""
            }
          }
      },created:function(){
          this.selectshuxingAll();
          this.queryType();
      },methods:{
        toadd:function() {
          this.addFormFlag = true;
        },addShopData:function () {
            this.$refs['valueform'].validate((valib)=>{
                if(valib == true){
                  ajax.post("http://127.0.0.1:8080/api/shuxing/insertshuxing",qs.stringify(this.addForm)).then(rs=>{
                    if(rs.data.code == 200){
                      this.$message({
                        type: 'success',
                        message: '新增成功!'
                      });
                      this.selectshuxingAll();
                    }
                    this.addFormFlag = false;
                  });
                }
            })

        },selectshuxingAll:function (page) {
            var data = {page:page,name:this.name}
            var size = this.size;
            var start = this.start;
            var ShuxingData = this;

          ajax.post("http://127.0.0.1:8080/api/shuxing/selectshuxingAll?start="+start+"&size="+size,qs.stringify(data)).then(rs=>{
            ShuxingData.ShuxingData = rs.data.data;
            ShuxingData.count = rs.data.count;
            ShuxingData.totalPage = Math.ceil(ShuxingData.count/size);
          });
        },del:function (id) {
          ajax.post("http://127.0.0.1:8080/api/shuxing/deleteshuxing?id="+id).then(rs=>{
            if(rs.data.code == 200){
                this.$message({
                  type: 'success',
                  message: '删除成功!'
                });
                this.selectshuxingAll();
            }
          });
        },fmt:function (a,b,c,d) {
          return c==0?"下拉框":c==1?"单选框":c==2?"复选框":"输入框";
        },ftype:function (a,b,c,d) {
          for (let i = 0; i <this.typeData.length ; i++) {
            if(c == this.typeData[i].id){
              return this.typeData[i].name;
            }
          }
          return "";
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
                debugger
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
          },toupdate:function (sx) {
            this.updFormFlag = true;
            this.updForm = sx;

        },weihuvalue:function (row) {
            this.updForm = row;
            this.sxFormFlag = true;
            debugger
            for (let i = 0; i <this.typeData.length ; i++) {
              if(row.typeid == this.typeData[i].id){
                this.shuxingtitle = this.typeData[i].name+"信息维护";
              }
            }
            var sxvalueData = this;
            ajax.post("http://127.0.0.1:8080/api/shuxingvalue/selectsxvalue?id="+this.updForm.id).then(rs=>{
              sxvalueData.sxvalueData = rs.data.data;
            });
        },delvalueId:function (id) {
          ajax.post("http://127.0.0.1:8080/api/shuxingvalue/deletesxvalueById?id="+id).then(rs=>{
            if(rs.data.code == 200){
              this.$message({
                type: 'success',
                message: '删除成功!'
              });
              this.weihuvalue(this.updForm);
            }
          });
        },toupdatevalue:function (sx) {
            this.updSx= sx;
            this.updShuXing = true;
        },toaddvalue:function () {
            this.addShuXing = true;
        },submitSx:function () {
          this.addSx.attid=this.attid;
          this.$refs['addformshuxing'].validate((valid)=>{
            if(valid == true){
              ajax.post("http://127.0.0.1:8080/api/shuxingvalue/insertsxvalue",qs.stringify(this.addSx)).then(rs=>{
                if(rs.data.code == 200){
                  this.$message({
                    type: 'success',
                    message: '新增成功!'
                  });
                  this.addShuXing = false;
                  this.weihuvalue(this.updForm);
                }
              })
            }
          })
        },updateSx:function () {
          ajax.post("http://127.0.0.1:8080/api/shuxingvalue/insertsxvalue",qs.stringify(this.updSx)).then(rs=>{
            if(rs.data.code == 201){
              this.$message({
                type: 'success',
                message: '修改成功!'
              });
              this.updShuXing = false;
              this.weihuvalue(this.updForm);
            }
          })
        },updShopData:function () {
          ajax.post("http://127.0.0.1:8080/api/shuxing/updateshuxing",qs.stringify(this.updForm)).then(rs=>{
            if(rs.data.code == 200){
              this.$message({
                type: 'success',
                message: '修改成功!'
              });
              this.selectshuxingAll();
            }
            this.updFormFlag = false;
          });
        }


        
      }
    }
</script>

<style scoped>

</style>
