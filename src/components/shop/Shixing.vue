<template>
  <div>


    <!--属性value-->
    <el-dialog title="属性值信息" :visible.sync="sxFormFlag">
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
            <el-button type="primary" icon="el-icon-edit"   @click="toupdatevalue(scope.row)"></el-button>
            <el-button type="danger" icon="el-icon-delete"  @click="delvalueId(scope.row.id)"></el-button>
          </template>
        </el-table-column>
      </el-table>

    </el-dialog>

    <el-dialog title="新增属性" :visible.sync="addShuXing">

      <el-form :model="addSx" ref="addForm" label-width="100px">

        <el-form-item label="属性英文名" prop="name">
          <el-input v-model="addSx.name" autocomplete="off" ></el-input>
        </el-form-item>

        <el-form-item label="属性中文名" prop="nameCH">
          <el-input v-model="addSx.namech" autocomplete="off" ></el-input>
        </el-form-item>

      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="submitSx">提 交</el-button>
        <el-button @click="addShuXing = false">取 消</el-button>
      </div>
    </el-dialog>

    <el-dialog title="修改属性" :visible.sync="updShuXing">

      <el-form :model="updSx" ref="addForm" label-width="100px">

        <el-form-item label="属性英文名" prop="name">
          <el-input v-model="updSx.name" autocomplete="off" ></el-input>
        </el-form-item>

        <el-form-item label="属性中文名" prop="nameCH">
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
          <el-button type="success" @click="weihuvalue(scope.row)">维护信息</el-button>
        </template>
      </el-table-column>
    </el-table>

    <font style="color: #8c939d">总数据:{{count}}条</font>
    <font v-for="page in totalPage">
      <el-button size="mini" @click="selectshuxingAll(page)">{{page}}</el-button>
    </font>

    <!--新增模板-->
    <el-dialog title="品牌信息" :visible.sync="addFormFlag">

      <el-form :model="addForm" ref="addForm" label-width="100px">

        <el-form-item label="属性英文名" prop="name">
          <el-input v-model="addForm.name" autocomplete="off" ></el-input>
        </el-form-item>

        <el-form-item label="属性中文名" prop="nameCH">
          <el-input v-model="addForm.namech" autocomplete="off" ></el-input>
        </el-form-item>

       <el-form-item label="商品类型" prop="typeId">
          <el-select v-model="addForm.typeid" placeholder="请选择">
            <el-option
              v-for="item in bandData"
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

        <el-form-item label="是否为SKU" prop="isSKU">
          <el-radio v-model="addForm.issku" :label="0">是</el-radio>
          <el-radio v-model="addForm.issku" :label="1">否</el-radio>
        </el-form-item>

      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="addFormFlag = false">取 消</el-button>
        <el-button type="primary" @click="addShopData">确 定</el-button>
      </div>
    </el-dialog>


    <!--新增模板-->
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
            <el-option v-for="item in bandData" :key="item.id" :label="item.name" :value="item.id">
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
          return{
            name:'',
            sxvalueData:[],
            bandData:[],
            typeData:[],
            arr:'',
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
            },addSx:{
              name:"",
              namech:"",
              attid:""
            },updSx:{
              name:"",
              namech:"",
              attid:""
            }
          }
      },created:function(){
          this.selectshuxingAll();
          this.queryType();
      },methods:{
        toadd:function() {
          this.addFormFlag = true;

        },addShopData:function () {
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
        },diguiNode: function (node) {
          // 判断是否为父节点
          var bf = this.isParent(node);
          if (bf == true) {
            for (let i = 0; i < this.typeData.length; i++) {
              //判断是否为当前节点的子节点
              if (node.id == this.typeData[i].pid) {
                this.diguiNode(this.typeData[i]);
              }
            }
          }
          if (bf == false) {
            for (let i = 0; i <this.typeData.length ; i++) {
              if(node.pid==this.typeData[i].id){
                this.arr='{"id":'+node.id+',"name":'+'"分类/'+this.typeData[i].name+"/"+node.name+'"'+'}';
                this.bandData.push(JSON.parse(this.arr))
              }
            }
          }
        },isParent: function (node) {// 判断是否为父节点  pid 有没有指向当前id
          for (let i = 0; i < this.typeData.length; i++) {
            if (node.id == this.typeData[i].pid) {
              return true;
            }
          }
          return false;
        },queryType:function () {
          var typeData = this;
          ajax.get("http://127.0.0.1:8080/api/stype/selectstypeAll").then(rs=>{
            typeData.typeData = rs.data.data;
            for (let i = 0; i < rs.data.data.length; i++) {
              if (rs.data.data[i].pid ==0) {
                this.diguiNode(rs.data.data[i]);
                break;
              }
            }
          });
        },toupdate:function (sx) {
            this.updFormFlag = true;
            this.updForm = sx;

        },weihuvalue:function (sx) {
            this.sxFormFlag = true;
            this.addSx.attid = sx.id;
            var sxvalueData = this;
            ajax.post("http://127.0.0.1:8080/api/shuxingvalue/selectsxvalue?id="+sx.id).then(rs=>{
              sxvalueData.sxvalueData = rs.data.data;
            });
        },delvalueId:function (id) {
          ajax.post("http://127.0.0.1:8080/api/shuxingvalue/deletesxvalueById?id="+id).then(rs=>{
            if(rs.data.code == 200){
              this.$message({
                type: 'success',
                message: '删除成功!'
              });
            }
          });
        },toupdatevalue:function (sx) {
            this.updSx= sx;
            this.updShuXing = true;
        },toaddvalue:function () {
            this.addShuXing = true;
        },submitSx:function () {
          ajax.post("http://127.0.0.1:8080/api/shuxingvalue/insertsxvalue",qs.stringify(this.addSx)).then(rs=>{
            if(rs.data.code == 200){
              this.$message({
                type: 'success',
                message: '新增成功!'
              });
            }
          })
        },updateSx:function () {
          ajax.post("http://127.0.0.1:8080/api/shuxingvalue/insertsxvalue",qs.stringify(this.updSx)).then(rs=>{
            if(rs.data.code == 201){
              this.$message({
                type: 'success',
                message: '修改成功!'
              });
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
