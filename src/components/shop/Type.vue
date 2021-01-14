<template>
      <div>
        <h1>商品分类管理</h1>
        <el-tree
          show-checkbox
          :data="data"
          :props="defaultProps"
          accordion>
          <span class="custom-tree-node" slot-scope="{ node, data }">
          <span>{{ node.label }}</span>
            <span>
              <el-button
                type="text"
                size="mini"
                @click="() => append(data)">
                Add
              </el-button>
              <el-button
                type="text"
                size="mini"
                @click="() => remove(node,data)">
                Del
              </el-button>
               <el-button
                 type="text"
                 size="mini"
                 @click="() => update(node,data)">
                Upd
              </el-button>
            </span>
          </span>
        </el-tree>



          <el-dialog title="新增节点" :visible.sync="dialogFormVisible">

            <el-form ref="form" label-width="80px">

              <el-form-item label="名称" prop="carname">
                <el-input v-model="form.name" autocomplete="off" ></el-input>
              </el-form-item>

            </el-form>
            <div slot="footer" class="dialog-footer">
              <el-button @click="dialogFormVisible = false">取 消</el-button>
              <el-button type="primary" @click="add">确 定</el-button>
            </div>
          </el-dialog>


        <el-dialog title="修改节点" :visible.sync="dialogFormupdVisible">

          <el-form ref="upd" label-width="80px">

            <el-form-item label="名称" prop="carname">
              <el-input v-model="upd.name" autocomplete="off" ></el-input>
            </el-form-item>

          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="dialogFormupdVisible = false">取 消</el-button>
            <el-button type="primary" @click="updtree">确 定</el-button>
          </div>
        </el-dialog>

      </div>
</template>

<script>
  import qs from 'qs';
  import ajax from 'axios';
    export default {
        name: "Type",
        data(){
          return{
              dialogFormVisible: false,
              dialogFormupdVisible: false,
              data:[],
              nodeData:[],
              nodeStr:'',
              defaultProps:{
                children: 'children',
                label: 'label'
              },
              form:{
                  name:'',
                  pid:''
              },
              upd:{
                  name:'',
                  id:''
              }
          }
        },methods:{
            chandleData:function () {
              for (let i = 0; i <this.nodeData.length ; i++) {
                if(this.nodeData[i].pid == 0){
                    this.diguiNode(this.nodeData[i]);
                  break;
                }
              }
              console.log(this.nodeStr);
              this.data.push(JSON.parse(this.nodeStr));

            },diguiNode:function (node) {
                  let parenta = this.isParent(node);
                  if(parenta == true){
                    let count = 0;
                    this.nodeStr+='{"id":'+node.id+',"label":"'+node.name+'","children":[';
                    for (let i = 0; i <this.nodeData.length ; i++) {
                      if(node.id == this.nodeData[i].pid){
                        count++;
                        this.diguiNode(this.nodeData[i]);
                        this.nodeStr+=",";
                      }
                    }
                    if(count != 0){
                      this.nodeStr = this.nodeStr.substr(0,this.nodeStr.length-1);
                    }
                    this.nodeStr+="]}";
                  }else{
                      this.nodeStr+='{"id":'+node.id+',"label":"'+node.name+'"}';
                  }
            },isParent:function (node) {
                for (let i = 0; i <this.nodeData.length ; i++) {
                    if(node.id == this.nodeData[i].pid){
                      return true;
                    }
                }
                return false;
            },remove:function (node, data) {
              debugger
                    if(node.childNodes.length<=0){
                      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
                        confirmButtonText: '确定',
                        cancelButtonText: '取消',
                        type: 'warning'
                      }).then(()=>{
                          ajax.post("http://192.168.1.1:8080/api/stype/stypeDeleteId?id="+node.id).then(rs=>{
                              if(rs.data.code == 200){
                                this.$message({
                                  type: 'success',
                                  message: '删除成功!'
                                });
                              }
                          })
                        }
                      )
                    }else{
                      this.$confirm("请先删除子节点");
                    }
            },append:function(data){
                  this.dialogFormVisible = true;
                  this.form.pid = data.id;
                  console.log(data.id);
                  debugger
            },add:function () {
                ajax.post("http://192.168.1.1:8080/api/stype/selectstypeInsert",qs.stringify(this.form)).then(rs=>{
                  if(rs.data.code == 200){
                    this.dialogFormVisible = false;
                    this.$message({
                      type: 'success',
                      message: '新增成功!'
                    });
                  }
                })
            },update:function (node, data) {
                  this.dialogFormupdVisible = true;
                  this.upd.name = data.label;
                  this.upd.id = data.id;
            },updtree:function () {
                ajax.post("http://192.168.1.1:8080/api/stype/selectstypeInsert",qs.stringify(this.upd)).then(rs=>{
                  if(rs.data.code == 200){
                    this.dialogFormupdVisible = false;
                    this.$message({
                      type: 'success',
                      message: '修改成功!'
                    });
                  }
                })
            }
      },created:function(){
           ajax.get("http://192.168.1.1:8080/api/stype/selectstypeAll").then(rs=>{
            this.nodeData = rs.data.data;
            this.chandleData();
            console.log(this.nodeData);
          }).catch(rs=>console.log(rs))
      }
    }
</script>

<style scoped>

</style>
