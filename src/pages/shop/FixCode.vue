<template>
    <section>
        <common-table
                :queryapi="queryapi"
                :tableheight="tableheight"
                :fieldsstr="fieldsstr"
                :tableitems="tableitems"
                :btswidth="btswidth"
                :href = "href"
                :hideAdd="hideAdd"

                :orderfield="orderfield"

                :hide-export="hideExport"
                :hide-options="hideOptions"
                :searchtitle="searchtitle"

                :showCustomizeAdd="showCustomizeAdd"
                v-on:customizeadd="customizeadd"

                :hideTool="hideTool"

                :addtitle="addtitle"
                :addapi="addapi"
                :editapi="editapi"

                :showCode="showCode"

                :hideSearch="hideSearch"
                :showEdit="showEdit"
                :showdelete="showdelete"

                v-on:selfExport="selfExport"
                ref="bolinkuniontable"
        ></common-table>

        <el-dialog
                :title="addtitle"
                :visible.sync="showRegisPark"
                width="30%">
            <el-form ref="addFormPark" label-width="120px" style="margin-bottom:-30px"
                     :model="addFormPark" :rules="addFormRules">

                  <el-form-item label="名称"  :prop="name">
                     <el-input v-model="addFormPark.name" style="width:90%" placeholder=""></el-input>
                 </el-form-item>

                 <el-form-item label="减免类型">
                    <el-select v-model="reducetype" style="width:90%">
                        <el-option
                                v-for="item in reduceType"
                                :label="item.value_name"
                                :value="item.value_no"
                        >
                        </el-option>
                    </el-select>
                </el-form-item>
                <el-form-item label="单张额度" v-if="showamount" :prop="amount_limit">
                    <el-input v-model="addFormPark.amount_limit" style="width:90%" placeholder=""></el-input>
                </el-form-item>
                <el-form-item label="总张数" v-if="showfree" :prop="free_limit">
                    <el-input v-model="addFormPark.free_limit" style="width:90%" placeholder=""></el-input>
                </el-form-item>
                <el-form-item label="固定码有效期" prop="validite_time">
                    <el-input v-model="addFormPark.validite_time" style="width:90%" placeholder="单位为小时"></el-input>
                </el-form-item>
                 <el-form-item label="状态" >
                    <el-select v-model="state" style="width:90%">
                        <el-option
                                v-for="item in stateList"
                                :label="item.value_name"
                                :value="item.value_no"
                        >
                        </el-option>
                    </el-select>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="showRegisPark = false" size="small">取 消</el-button>
                <el-button type="primary" size="small" @click="handleAdd" :loading="addloading">确 定</el-button>
            </span>
        </el-dialog>


    </section>
</template>


<script>
    import {path, checkURL, checkUpload, checkNumber, payType,stateType} from '../../api/api';
    import util from '../../common/js/util'
    import common from '../../common/js/common'
    import {AUTH_ID} from '../../common/js/const'
    import CommonTable from '../../components/CommonTable'

    export default {
        components: {
            CommonTable
        },
        data() {
            return {
                loading: false,
                hideExport: true,
                hideSearch: true,
                orderfield:'id',
                tableheight: '',
                showdelete: false,
                hideOptions: false,
                addloading:false,

                showCode:true,
                hideAdd: true,

                addFormPark: {
                    validite_time:'24',
                    amount_limit:'',
                    free_limit:'',
                },
                showRegisPark: false,
                showCustomizeAdd:true,

                showamount:true,
                showfree:true,
                state:'可用',
                reducetype:'减免券',
                reduceType:[
                    { 'value_name': '减免券','value_no': 1},
                    { 'value_name': '全免券','value_no': 2},

                ],
                stateList :[
                     {'value_name': '可用','value_no': 0},
                     {'value_name': '不可用','value_no': 1},
                 ],

                hideTool: false,
                showEdit: true,
                queryapi: '/fixcode/query',
                selfexportapi:'/fixcode/downloadCode',
                addapi:'/fixcode/add',
                editapi:'/fixcode/edit',
                btswidth: '160',
                href:'https://www.baidu.com/s?wd=node-pre-gyp+install+--fallback-to-build&ie=UTF-8&tn=39042058_20_oem_dg',
                fieldsstr: 'id__park_id__operate_time__ticketfree_limit__ticket_limit__ticket_money__operate_type__add_money',
                tableitems: [
                 {
                       hasSubs: false, subs: [
                           {
                               label: '名称',
                               prop: 'name',
                               width: '123',
                               type: 'str',
                               editable: false,
                               searchable: true,
                               addable: true,
                               hidden:'',
                               unsortable: true,
                               align: 'center',
                           },
                       ]
                   },
                   {
                       hasSubs: false,
                       subs: [{
                           label: '创建日期',
                           prop: 'create_time',
                           width: '180',
                           type: 'date',
                           editable: false,
                           searchable: true,
                           addable: true,
                           unsortable: true,
                           align: 'center',
                           format: function (row) {
                               return common.dateformat(row.create_time)
                           }
                       }]
                   },
                   {
                         hasSubs: false,
                         subs: [{
                             label: '有效期/小时',
                             prop: 'validite_time',
                             width: '180',
                             type: 'str',
                             editable: false,
                             searchable: false,
                             addable: true,
                             unsortable: true,
                             hidden: "",
                             align: 'center',
                         }]
                     },
                   {
                       hasSubs: false, subs: [
                           {
                               label: '剩余时长',
                               prop: 'time_limit',
                               width: '123',
                               type: 'str',
                               editable: false,
                               searchable: true,
                               addable: true,
                               hidden:'',
                               unsortable: true,
                               align: 'center',
                           },
                       ]
                   },
                  {
                     hasSubs: false, subs: [
                          {
                              label: '剩余金额',
                              prop: 'money_limit',
                              width: '123',
                              type: 'str',
                              editable: false,
                              searchable: true,
                              addable: true,
                              hidden:'',
                              unsortable: true,
                              align: 'center',
                          },
                      ]
                  },
                    {
                       hasSubs: false, subs: [
                           {
                               label: '剩余张数',
                               prop: 'free_limit',
                               width: '123',
                               type: 'str',
                               editable: false,
                               searchable: true,
                               addable: true,
                               unsortable: true,
                               align: 'center',
                           },
                       ]
                   },
                       {

                         hasSubs: false,

                         subs: [{
                             label: '状态',
                             prop: 'state',
                             width: '100',
                             type: 'selection',
                             selectlist:stateType,
                             editable: true,
                             searchable: true,
                             addable: true,

                             unsortable: true,
                             align: 'center',
                             format:(row) => {
                                if(row.state==0){

                                    return '可用'
                                }else if(row.state==1){

                                    return '不可用'
                                }else{

                                    return '已过期'
                                }
                             }

                             //format: (row) => {

                                 //这里注意，一定要使用箭头函数，因为箭头函数中的this是延作用域向上取到最近的一个
                                 //也就是data中的this,可以获取到this.aroles
                                 //如果是普通函数，this.aroles获取到的是undefined,因为this的作用域是本身，并没有aroles这个变量
                               //  return common.nameformat(row, stateType, 'state');
                                 //return common.nameformat(row, genderType, 'sex');
                             //}
                         }]
                     },
                     {

                        hasSubs: false,
                        subs: [{
                            label: '二维码链接',
                            prop: 'code_src',
                            width: '180',
                            type: 'str',
                            editable: false,
                            searchable: false,
                            addable: false,
                            unsortable: true,
                            align: 'center',

                        }]
                    },

                ],
                searchtitle: '查询明细',
                addtitle:"添加",



                amount_limit:"amount_limit",
                free_limit:"free_limit",
                validite_time:"validite_time",
                name:"name",

                addFormRules: {
                    name: [
                        {required: true, message: '请输入固定码名称', trigger: 'blur'}
                    ],
                    amount_limit: [
                        {required: true, message: '请输入总额度', trigger: 'blur'}
                    ],
                    free_limit:[
                      {required: true, message: '请输入总张数', trigger: 'blur'}
                    ],
                    validite_time:[
                      {required: true, message: '请输入固定码有效期', trigger: 'blur'}
                    ],
                }
            }
        },
         methods: {

            customizeadd: function () {
                //显示注册新车场
                this.showRegisPark = true;

                this.addressTitle = '';
                this.addFormPark.validite_time='24';
            },

            selfExport(params){
                var api = this.selfexportapi;
                 window.open(path + api + '?'+params);
            },

            handleAdd(){
                let _this = this;

                this.$refs.addFormPark.validate((valid) => {

                    if (valid) {
                        _this.addloading = true;
                        let aform = _this.generateForm(_this.addFormPark);
                        _this.$axios.post(path + _this.addapi, _this.$qs.stringify(aform), {
                            headers: {
                                'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8'
                            }
                        }).then(function (response) {
                            let ret = response.data;

                            if (ret > 0 || ret.state === 1) {
                                //更新成功
                                _this.$refs['bolinkuniontable'].getTableData({});
                                _this.$message({
                                    message: '添加成功!',
                                    type: 'success',
                                    duration: 600
                                });
                                _this.showRegisPark = false;
                                _this.addFormPark = {};
                            } else {
                                //更新失败
                                _this.$message({
                                    message: ret.msg,
                                    type: 'error',
                                    duration: 1200
                                });
                            }
                            _this.addloading = false

                        }).catch(function (error) {
                            //更新失败
                            _this.$message({
                                message: '请求失败!' + error.data,
                                type: 'error',
                                duration: 1200
                            });
                            _this.addloading = false;
                        })
                    }
                })
            },
            generateForm(sform) {
                //用来构建相同的参数
                //sform.token = common.attachParams('token');
                //sform.groupid = common.attachParams('groupid', 1);
                //sform.cityid = common.attachParams('cityid', 1);
                //sform.unionid = common.attachParams('unionid', 1);
                //sform.channelid = common.attachParams('channelid', 1);
                //sform.loginuin = common.attachParams('loginuin', 1);
                //sform.ishdorder = common.attachParams('ishdorder', 1);
                //sform.roleid = common.attachParams('loginroleid', 1);
                sform.shopid = common.attachParams('shopid', 1);
                sform.type = this.reducetype;
                sform.state = this.state;
                return sform;
            },
            getShopAccountInfo(){
                  let vm = this;
                  vm.$axios.post(path+"/shopaccount/shopinfo?id="+sessionStorage.getItem('shopid'),{
                      headers: {
                          'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8'
                      }
                  }).then(function (response) {
                    let ret = response.data;
                    //var ret = eval('('+result+')')
                    //vm.account=ret;

                    //vm.shopname=ret.name
                    if(ret.ticket_unit==1||ret.ticket_unit==2||ret.ticket_unit==3){
                        //时长
                        //alert('nihao');
                        //alert(vm.tableitems[3].subs[0].hidden)
                        vm.tableitems[4].subs[0].hidden = "true";
                        //alert('nihuai');
                    }

                    else if(ret.ticket_unit==4){
                        //金额  隐藏剩余时长
                        vm.tableitems[3].subs[0].hidden = "true";
                     }

                     if(ret.support_type==0){
                        vm.reduceType=[
                            { 'value_name': '减免券','value_no': 1},
                        ]
                     }

                  });
                },
        },
        mounted() {
            window.onresize = () => {
                this.tableheight = common.gwh() - 143;
            }
            this.tableheight = common.gwh() - 143;
        },
        activated() {
            this.getShopAccountInfo()
            window.onresize = () => {
                this.tableheight = common.gwh() - 143;
            }
            this.tableheight = common.gwh() - 143;
            //this.addFormPark.validite_time=24;
            this.$refs['bolinkuniontable'].$refs['search'].resetSearch()
            this.$refs['bolinkuniontable'].getTableData({})
        },
        watch: {
          reducetype : function (val) {
                if(val==1){//时长减免

                    this.showamount = false;
                    this.showfree = false;
                    this.addFormPark.validite_time='24';
                    this.showamount = true;
                    this.showfree = true;

                }else{//全免
                    this.showamount = false;
                    this.showfree = false;
                    this.addFormPark.validite_time='24'
                    this.showfree = true;
                }
            },
        },
    }

</script>

<style>
    .gutter {
        display: none
    }
</style>
