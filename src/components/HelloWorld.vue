<template>
  <div id="classGroup">
      <!--分区类别分组(分区组)-->
        <Modal v-model="group_Modal" :mask-closable="false" draggable scrollable width="900" title="分区类别分组">
            <Form ref="formAddData" :model="formAddData" label-position="right" :label-width="130">
                <div class="group_item" v-for="(items,index) in historyDataList" :key="index">
                    <div class="titles">
                        <span> {{items.areaName}}: </span>
                    </div>
                    <div style="padding:10px;">
                        <Checkbox
                            :indeterminate="indeterminate"
                            :value="checkAll"
                            @on-change="handleCheckAll(items)">全选</Checkbox>
                    </div>
                    <CheckboxGroup v-model="saveGroupList" @on-change="checkAllGroupChange">
                        <Checkbox v-for="(areaItem,index) in items.list" :key="index" :label="areaItem.areaId">{{areaItem.areaName}}</Checkbox>
                    </CheckboxGroup>           
                </div>
            </Form>

            <!-- 保存--取消按钮  -->
            <div slot="footer">
                <Row :gutter="24" type="flex" justify="end">
                    <Col span="3"> 
                        <i-button type="primary" size="large" long v-on:click="submitAdd_areaGroup()">保存</i-button>
                    </Col>
                    <Col span="3"> 
                        <Button size="large" long @click="cancelAdd_areaGroup()">取消</Button>
                    </Col>
                </Row>
            </div>
        </Modal>
  </div>
</template>

<script>
import tabData from "./data.json"
export default {
    props:['historyData'],
    data(){
        return{
            historyDataList: [], // 页面展示的数据
            //新增初始化变量
            formAddData: {},
            group_Modal:true,
            indeterminate: true,
            checkAll: false,
            saveGroupList: [], //选中后保存的数组list
            //接口保存用的变量
            saveList:{
                areaIds:[],
                groupId: "",
            },
        }
    },
    methods:{
        //全选按钮
        handleCheckAll (items) {
            console.log("items:",items)
            if( items.areaType == "1" ){
                let arr1 = [];
                items.list.map(v=>{
                  arr1.push(v.areaId)
                })
                console.log("00000",arr1,this.saveGroupList)
                if( arr1.length == this.saveGroupList.length){
                  this.checkAll = true;
                }else {
                    this.checkAll = false;
                }
                this.indeterminate = false;
                // if (this.indeterminate) {
                //     this.checkAll = false;
                // }else {
                //     this.checkAll = !this.checkAll;
                // }
                // this.indeterminate = false;

            }else if(items.areaType == "2" ){

            }else if(items.areaType == "3" ){
                
            }
            // if (this.indeterminate) {
            //     this.checkAll = false;
            // }else {
            //     this.checkAll = !this.checkAll;
            // }
            // this.indeterminate = false;

            // if (this.checkAll) {
            //     this.saveGroupList = this.saveGroupList;
            // } else {
            //     this.saveGroupList = [];
            // }
        },
        //chexked 按钮
        checkAllGroupChange (data) {

            console.log("checkAllGroupChange:",data)
            this.saveGroupList = data;


            // let arr1 = [];
            // this.historyDataList.map(v=>{
            //     v.list.map(item=>{
            //         arr1.push(item.areaId)
            //     })
            //     console.log("000000:",data,arr1)
            // })
            // if( data.length == arr1.length ){
            //   this.checkAll = true;
            // }



            // if (data.length === this.historyDataList.list.length) {
            //     this.indeterminate = false;
            //     this.checkAll = true;
            // } else if (data.length > 0) {
            //     this.indeterminate = true;
            //     this.checkAll = false;
            // } else {
            //     this.indeterminate = false;
            //     this.checkAll = false;
            // }
            


        },

        //保存按钮
        submitAdd_areaGroup(){
            if(this.saveGroupList){
                this.saveList.areaIds = this.saveGroupList;
                this.saveList.groupId =  this.historyData.groupId;
            }else{
               this.saveList = {}; 
            }
            this.$axios({
                method: 'post',
                data: this.saveList,
                params:{
                    userId: this.$store.state.user.userId,
                    userName: this.$store.state.user.userName, 
                },
                headers: {'Content-Type': 'application/json'},
                url: this.baseURL + '/area/areaGroupDetails/insertGroupDetailsList',
            })
            .then((res)=> {
                if(res.status == "200"){
                    if (res.data.code === "0") {
                        this.$Message.success('您已成功增加一条分区组数据！');
                    }else {
                        alert(res.data.msg)
                    }
                }
            }).catch((error)=> { console.log(error)})
        },
        //取消按钮
        cancelAdd_areaGroup(){

        },
        //车站下的分区list按分区类型分组
        get_arealist(){
            this.$axios({
                method: 'get',
                url: this.baseURL + '/area/areaGroup/areaList', 
                params: {
                    stationId: "0101",
                    userId: this.$store.state.user.userId,
                    userName: this.$store.state.user.userName,
                }
            })
            .then((res)=> {
                console.log("车站下的分区list按分区类型分组：",res.data.result);
                if(res.status == "200"){
                    if (res.data.code === "0") {

                        res.data.result.map(v=>{
                            // v.indeterminate =true;
                            v.checked = [];
                            // v.checkedAll = false;
                        })
                       this.historyDataList = res.data.result;
                       console.log("this.historyDataList:",this.historyDataList)
                    }else {
                        alert(res.data.msg)
                    }
                }

            }).catch((error) =>{})

        }
    },
    mounted(){

    },
    created(){
        // this.get_arealist();
        this.historyDataList = tabData;
        // this.historyDataList.map(v=>{
        //     v.checked = [];
        //     // v.checkedAll = false;
        // })
        console.log("this.historyDataList:",this.historyDataList)

    },
    watch:{
        // historyData:function(val){
        //     this.formAddData = val;
        //     console.log("分区组：",this.historyData.stationId,this.historyData.groupId)
        // },
    }
}
</script>

<style type="text/css">
    /* 无遮罩层，拖拽功能不让点其他区域样式 */
    .ivu-modal-no-mask {pointer-events: auto !important;}
    .group_item{
        border: 1px solid #e9e9e9;
        margin-bottom: 20px;
    }
    .group_item .titles{
        border-bottom: 1px solid #e9e9e9;
        padding:10px;
        font-size: 18px;
        font-weight: bold;
    }
    .ivu-checkbox-group {
        display: inline-block;
        font-size: 14px;
        padding: 10px;
    }
</style>