<template>
  <div>

    <el-button type="success" @click="toadd">新增</el-button>


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
        label="英文名">
      </el-table-column>

      <el-table-column
        prop="namech"
        label="名称"
        width="180">
      </el-table-column>

      <el-table-column
        prop="typeid"
        label="商品类型"
        :formatter="changetypeid"
      >
      </el-table-column>

      <el-table-column
        prop="type"
        label="属性类型"
        :formatter="changetype">
      </el-table-column>

      <el-table-column
        prop="id"
        label="操作">
        <template slot-scope="scope">
          <el-button type="primary" icon="el-icon-edit"   @click="toupdate(scope.row)"></el-button>
          <el-button type="danger" icon="el-icon-delete"  @click="del(scope.row.id)"></el-button>
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



       <!-- <el-form-item label="商品类型" prop="typeId">
          <el-select v-model="addForm.typeid" placeholder="请选择">
            <el-option
              v-for="item in TypeDatas"
              :key="item.id"
              :label="item.name"
              :value="item.id">
            </el-option>
          </el-select>
        </el-form-item>-->

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

  </div>
</template>

<script>
  import ajax from 'axios';
  import qs from 'qs';
    export default {
        name: "Shixing",
      data(){
          return{
            ShuxingData:[],
            count:0,
            start:1,
            size:3,
            page:0,
            totalPage:0,
            addFormFlag:false,
            addForm:{
              name:"",
              namech:"",
              type:"",
              issku:""
            }

          }
      },created:function(){
          this.selectshuxingAll();
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
              }
              this.addFormFlag = false;
            });
        },selectshuxingAll:function (page) {
            var data = {page:page}
            var size = this.size;
            var start = this.start;
            var ShuxingData = this;
          ajax.post("http://127.0.0.1:8080/api/shuxing/selectshuxingAll?start="+start+"&size="+size,qs.stringify(data)).then(rs=>{
            ShuxingData.ShuxingData = rs.data.data;
            ShuxingData.count = rs.data.count;
            ShuxingData.totalPage = Math.ceil(ShuxingData.count/size);
          });
        },changetypeid:function () {
          
        },changetype:function () {
          
        },del:function (id) {
          ajax.post("http://127.0.0.1:8080/api/shuxing/deleteshuxing?id="+id).then(rs=>{
            if(rs.data.code == 200){
                this.$message({
                  type: 'success',
                  message: '删除成功!'
                });
            }
              /*this.$router.go(0)*/
          });
        }
      }
    }
</script>

<style scoped>

</style>
