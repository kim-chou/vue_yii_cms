<template>
  <div>
    <div class="row h44">
      <div class="col-xs-6">

      </div>
      <div class="col-xs-6 flex-end-center">
        <button-box type="add"  @click="addItem" title="添加顶级类型"></button-box>
        <button-box type="edit" @click="editItem"></button-box>
        <button-box type="delete" @click="deleteItem"></button-box>
      </div>
    </div>

    <table-box ref="tableBox" :url="table.url" :columns="table.columns" :key-name="table.keyName" :parse-data="table.parseData" :data="table.data" :multi-select="table.multiSelect">

        <tr slot="row"  slot-scope="props">
          <td class="tc">{{props.index}}</td>
          <td>{{props.item.title}}</td>
          <td>{{props.item.value}}</td>
          <td> <button-box class="btn-info btn-sm" :to="'/category/children?pid='+props.item.id+'&ptitle='+props.item.title">设置子类型</button-box></td>
        </tr>


    </table-box>

    <modal @ok="formSubmit" ref="modal">
      <form class="form-horizontal form-edit">

        <input type="hidden" v-model="form.data.id"/>
        <div class="form-group">
          <div class="col-xs-12">
            <input class="form-control form-field" placeholder="类型名称" v-model="form.data.title"/>
          </div>
        </div>

        <div class="form-group">
          <div class="col-xs-12">
            <input class="form-control form-field" placeholder="类型值" v-model="form.data.value"/>
          </div>
        </div>

      </form>
    </modal>
  </div>
</template>

<script>

  import api from '../../utils/config/api'
  import network from '../../utils/base/network'
  import formValidation from '../../utils/base/formValidation'

  import TableBox from '../../components/mod/TableBox'
  import ButtonBox from  '../../components/mod/ButtonBox'
  import Modal from '../../components/mod/Modal'
  import FormSubmit from "../../components/mod/FormSubmit"

  export default {
    name: 'CategoryIndex',
    components: {TableBox,ButtonBox,Modal,FormSubmit},

    data:function () {
      return {
        table:{
          url:api.category_index,
          multiSelect:false,
          keyName:'id',
          columns:[
            {
              title:"名称",
              field:"title",
              width:''
            },
            {
              title:"值",
              field:"value",
              width:''
            },
            {
              title:"操作",
              field:"caozuo",
              width:'200'
            },
          ],
          parseData:function (data) {
            data.forEach(function(row){

            })
          }
        },
        form:{
          data:{
            id:'',
            title:'',
            value:''
          },
          rules:{
            title:{
              required:true
            },
            value:{
              required:true
            }
          },
          message:{
            title:{
              required:"请输入类型名称"
            },
            value:{
              required:"请输入类型值"
            }
          }
        }
      }
    },
    mounted:function () {
      let breadcrumb=[
        {
          link: '#',
          title:'系统设置'
        },
        {
          link:'',
          title:'类型配置'
        },

      ]
      this.$store.commit('SET_BREADCRUMB',breadcrumb)
    },
    methods:{
      addItem:function(){
        this.form.data={
          id:'',
          title:'',
          value:''
        }
        this.$refs.modal.show()
      },
      deleteItem:function () {

        let table=this.$refs.tableBox
        let id=table.getSelectedRowId()[0]
        if(!id){
          this.$toast({
            title: '请选择一行数据'
          })
          return
        }

        this.$confirm({
          content:"删除操作不能恢复，您确定要删除吗",

          success:function () {
            let post_data={
              id: id,
              status:9,
              operation:'updateStatus'
            }
            network.post(api.category_save,post_data).then((res)=>{
              table.refresh()
            })
          },
        })


      },
      editItem:function () {
        let table=this.$refs.tableBox
        let data=table.getSelectedRowData()[0]
        if(!data){
          this.$toast({
            title: '请选择一行数据',
          })
          return
        }

        this.form.data={...data}
        this.$refs.modal.show()

      },
      formSubmit:function () {

        console.log("ov")
        let form_data={...this.form.data}

        let validation=formValidation.validate(this.form.rules,form_data,this.form.message)
        if(validation.code=='FAIL'){
          this.$toast({
            title:validation.msg,
            type:'error'
          })
          return false
        }

        network.post(api.category_save,{
          ...form_data
        }).then((res)=>{
          this.$toast({
            title:"保存成功"
          })
          let table=this.$refs.tableBox
          table.refresh()
        })


        this.$refs.modal.hide()

      }

    }
  }
</script>

<style lang="less" scoped>

</style>
