<template>
  <div>
    <div class="row h44">
      <div class="col-xs-6">

      </div>
      <div class="col-xs-6 flex-end-center">
        <button-box type="add" to="/course/edit" title="添加"></button-box>
        <button-box type="edit" @click="editItem"></button-box>
        <button-box type="delete" @click="deleteItem"></button-box>
      </div>
    </div>

    <table-box ref="tableBox" :url="table.url" :columns="table.columns" :key-name="table.keyName" :parse-data="table.parseData" :data="table.data" :multi-select="table.multiSelect">

        <tr slot="row"  slot-scope="props">
          <td class="tc">{{props.index}}</td>
          <td>
            <img :src="props.item.cover" width="50" height="50"/>
          </td>
          <td><router-link :to="'/course/edit?id='+props.item.id" class="title">{{props.item.name}}</router-link></td>
          <td class="tc">{{props.item.sort}}</td>
          <td><router-link :to="'/lesson/index?cid='+props.item.id+'&cname='+props.item.name" class="btn btn-info btn-sm">管理课程</router-link></td>
        </tr>

    </table-box>

  </div>
</template>

<script>

  import api from '../../utils/config/api'
  import config from '../../utils/config/config'
  import network from '../../utils/base/network'

  import TableBox from '../../components/mod/TableBox'
  import ButtonBox from  '../../components/mod/ButtonBox'

  export default {
    name: 'CourseIndex',
    components: {TableBox,ButtonBox},

    data:function () {
      return {
        table:{
          url:api.course_index,
          keyName:'id',
          columns:[
            {
              title:"封面",
              field:"cover",
              width:'80'
            },
            {
              title:"名称",
              field:"name",
              width:''
            },
            {
              title:"排序",
              field:"sort",
              width:'80'
            },
            {
              title:"操作",
              field:"caozuo",
              width:'100'
            },
          ],
          parseData:function (data) {
            data.forEach(function(row){

            })
          }
        },
      }
    },
    mounted:function () {
      let breadcrumb=[
        {
          link: '#',
          title:'内容管理'
        },
        {
          link:'',
          title:'教程管理'
        },

      ]
      this.$store.commit('SET_BREADCRUMB',breadcrumb)
    },
    methods:{
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
              operation:'delete'
            }
            network.post(api.course_save,post_data).then((res)=>{
              table.refresh()
            })
          },
        })


      },
      editItem:function () {
        let table=this.$refs.tableBox
        let id=table.getSelectedRowId()[0]
        if(!id){
          this.$toast({
            title: '请选择一行数据',
          })
          return
        }
        this.$router.push({path:'/course/edit',query:{
          id:id
        }})

      },
    },
    beforeRouteEnter:function (to,from,next) {
      //从编辑页面返回到列表，应该刷新一下当前页面
      if(from.path=='/course/edit'){
        next(vm=>{
          vm.$refs.tableBox.refresh()
        })
      }
      next()
    }
  }
</script>

<style lang="less">
  .btn-dropdown{
    .dropdown-toggle{
      padding: 0 !important;
      &:hover{
        background-color: white !important;
      }
      .open&{
        background-color: white !important;
      }
    }
  }

  .status{
    .btn{
      padding: 3px 5px !important;
      margin-top: 5px;
      margin-bottom: 5px;
    }
  }


</style>
