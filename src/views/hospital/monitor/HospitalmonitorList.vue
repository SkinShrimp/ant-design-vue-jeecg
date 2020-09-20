<template>
  <a-card :bordered="false">

<template>
  <div>
    <a-modal
      v-model="visible"
      title="详细情况"
      width="1100px"
      :ok-button-props="{ props: { disabled: true } }"
      :cancel-button-props="{ props: { disabled: true } }"
      @ok="handleOk"
    >
    <a-table
            ref="table"
            size="middle"
            :scroll="{x:true}"
            bordered
            :footer="null"
            rowKey="id"
            :columns="auditList"
            :dataSource="dataSources"
            :pagination="ipaginations"
            :loading="loading"
            class="j-table-force-nowrap"
            @change="">

            <span slot="action" slot-scope="text, record">
            <a @click="setModal1Visible(true)">审核</a>
                        <a-divider type="vertical" />
            <a @click="showModal(record)">详情</a>

            </span>
      </a-table>
    </a-modal>
  </div>
</template>

<!-- 详情框模板 路径:      详情-->
<template>
  <div>
    <a-modal
      title="详细情况"
      width="1100px"
      :ok-button-props="{ props: { disabled: true } }"
      :cancel-button-props="{ props: { disabled: true } }"
      @ok="handleOk"
    >
          <a-form-item
            :labelCol="labelCol"
            :wrapperCol="wrapperCol"
            label="部门角色名称">
            <a-input placeholder="请输入部门角色名称" v-decorator="" />
          </a-form-item>

    </a-modal>

  </div>
</template>

<!-- 批量抽查模板 路径: 批量抽查 -->
<template>
  <div id="">
    <a-modal
      title="批量抽查"
      style="top: 20px;text-align:center"
      width="400px"
      :visible="spotCheckModalVisible"
      @ok="() => startSpotCheck(this)"
      @cancel="() => setSpotCheckModalVisible(false)"
    >
      <a-form-model id="spotCheckForm"  @submit="handleSubmit"   :model="form" :label-col="labelCol" :wrapper-col="wrapperCol">
        <a-form-item label="抽查方式" >
          <a-radio-group id= "checkStatus" v-model="spotCheckForm.checkStatus" name="checkStatus" default-value="1">
            <a-radio value="1" @click="hiddenDateFlag()">马上抽查</a-radio>
            <a-radio value="0" @click="showDateFlag()">定时抽查</a-radio>
          </a-radio-group>
        </a-form-item>
        <a-form-model-item label="抽查时间间隔">
<!--          <a-select-->
<!--            placeholder="Select a person"-->
<!--            v-decorator="[-->
<!--              {rules: [{ required: true, message: '请选择执行人'}]}-->
<!--            ]"-->
<!--          >-->
<!--            <a-select-option value="jack">-->
<!--              Jack-->
<!--            </a-select-option>-->
<!--            <a-select-option value="lucy">-->
<!--              Lucy-->
<!--            </a-select-option>-->
<!--            <a-select-option value="tom">-->
<!--              Tom-->
<!--            </a-select-option>-->
<!--          </a-select>-->
          <a-select id="dateInterval" v-model="spotCheckForm.dateInterval" name="dateInterval" placeholder="请选择抽查时间间隔"  v-decorator="[ 'name', {rules: [{ required: true, message: '时间间隔不许为空!' }] }]">
            <a-select-option value="3">
              10分钟
            </a-select-option>
            <a-select-option value="4">
              20分钟
            </a-select-option>
            <a-select-option value="5">
              30分钟
            </a-select-option>
          </a-select>
        </a-form-model-item>
        <a-dropdown v-if="dateFlag">
          <a-form-item label="抽查开始时间" >
            <a-date-picker
              show-time
              format="YYYY-MM-DD HH:mm:ss"
              placeholder="请选择抽查日期"
              style="width: 100%;"
              v-model="spotCheckForm.dateStart"
            />
          </a-form-item>
        </a-dropdown>
      </a-form-model>


    </a-modal>
  </div>
</template>

        <!-- 审核框模板 路径:      审核->审核->审核-->
<template>
  <div id="components-modal-demo-position">
    <a-modal
      title="审核"
      style="top: 20px;text-align:center"
      width="400px"
      :visible="modal1Visible"
      @ok="() => setModal1Visible(false)"
      @cancel="() => setModal1Visible(false)"
    >

<template>
  <a-card :bordered="false" style=" ">

    <div class="table-page-search-wrapper">
      <a-form layout="inline" :form="form">
        <a-row :gutter="24">
          <a-col :span="20">
            <a-form-item label="上传状态">
              <a-select v-model="queryParam.outstatus" placeholder="请选择上传状态">
                <a-select-option value="1">人工正常</a-select-option>
                <a-select-option value="2">未上传</a-select-option>
              </a-select>
           </a-form-item>
          </a-col>
        </a-row>

        <a-row :gutter="24">
          <a-col :span="20">
            <a-form-item label="人脸识别">
              <a-select v-model="queryParam.outstatus" placeholder="请选择人脸识别">
                <a-select-option value="1">人工通过</a-select-option>
                <a-select-option value="2">未通过</a-select-option>
              </a-select>
           </a-form-item>
          </a-col>
        </a-row>

        <a-row :gutter="24">
          <a-col :span="20">
            <a-form-item label="定位状态">
              <a-select v-model="queryParam.outstatus" placeholder="请选择定位状态">
                <a-select-option value="1">人工正常</a-select-option>
                <a-select-option value="2">偏离</a-select-option>
              </a-select>
           </a-form-item>
          </a-col>
        </a-row>

        <a-row :gutter="24">
          <a-col :span="20">
            <a-form-item label="备注">
                <a-input placeholder="" v-model="queryParam.percode"></a-input>
           </a-form-item>
          </a-col>
        </a-row>

      </a-form>
    </div>

  </a-card>
</template>

    </a-modal>
  </div>
</template>

    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">

          <a-col :md="6" :sm="12">
            <a-form-item label="身份证号">
                <a-input placeholder="请输入身份证号查询" v-model="queryParam.percode"></a-input>
            </a-form-item>
          </a-col>

          <a-col :md="6" :sm="12">
            <a-form-item label="患者姓名">
                <a-input placeholder="请输入患者姓名查询" v-model="queryParam.name"></a-input>
            </a-form-item>
          </a-col>

          <a-col :md="6" :sm="8">
            <a-form-item label="住院类型">
              <a-select v-model="queryParam.outstatus" placeholder="请选择住院类型">
                <a-select-option value="1">在院</a-select-option>
                <a-select-option value="2">出院</a-select-option>
                <a-select-option value="4">抽查</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>

          <template v-if="toggleSearchStatus">
            <a-col :md="6" :sm="8">
              <a-form-item label="住院编号">
                <a-input placeholder="请输入住院编号名字" v-model="queryParam.incode"></a-input>
              </a-form-item>
            </a-col>

           <a-col :xl="6" :lg="7" :md="8" :sm="24">
                        <a-form-item label="住院时间">
                          <a-range-picker v-model="queryParam.indate"
                                          format="YYYY-MM-DD"
                                          :placeholder="['开始时间', '结束时间']"
                                          @change="onInDateChange" />
                        </a-form-item>
            </a-col>

            <a-col :md="6" :sm="8">
              <a-form-item label="医院名称">
                <a-input placeholder="请输入医院名称查询" v-model="queryParam.hisname"></a-input>
              </a-form-item>
            </a-col>

            <a-col :md="6" :sm="8">
              <a-form-item label="就诊科室">
                <a-select placeholder="请选择就诊科室" v-model="queryParam.dept">
                  <a-select-option v-for="dept in dictOptions" :key="dept.id" :value="dept.id">
                    {{ dept.name }}
                  </a-select-option>
                </a-select>

              </a-form-item>
            </a-col>




            <a-col :md="6" :sm="8">
              <a-form-item label="住院诊断">
                <a-input placeholder="请输入住院诊断查询" v-model="queryParam.diagnose"></a-input>
              </a-form-item>
            </a-col>

             <a-col :xl="6" :lg="7" :md="8" :sm="24">
                          <a-form-item label="出院时间">
                            <a-range-picker v-model="queryParam.outdate"
                                            format="YYYY-MM-DD"
                                            :placeholder="['开始时间', '结束时间']"
                                            @change="onOutDateChange" />
                          </a-form-item>

              </a-col>

          </template>

          <a-col :md="6" :sm="8">
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <a @click="handleToggleSearch" style="margin-left: 8px">
                {{ toggleSearchStatus ? '收起' : '展开' }}
                <a-icon :type="toggleSearchStatus ? 'up' : 'down'"/>
              </a>
            </span>
          </a-col>

        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->

    <!-- 操作按钮区域 -->
    <div class="table-operator">
      <a-button type="primary" icon="download" @click="handleExportXls('医院患者服务表')">导出</a-button>
      <a-button type="primary" key="1" icon="file-sync" @click="handleSpotCheck(selectedRowKeys,selectionRows)">批量抽查</a-button>

    </div>

    <!-- table区域-begin -->
    <div>
      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项
        <a style="margin-left: 24px" @click="onClearSelected">清空</a>
      </div>

      <a-table
        ref="table"
        size="middle"
        :scroll="{x:true}"
        bordered
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"
        :rowSelection="rowSelection"
        class="j-table-force-nowrap"
        @change="handleTableChange">

        <template slot="htmlSlot" slot-scope="text">
          <div v-html="text"></div>
        </template>
        <template slot="imgSlot" slot-scope="text">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">无图片</span>
          <img v-else :src="getImgView(text)" height="25px" alt="" style="max-width:80px;font-size: 12px;font-style: italic;"/>
        </template>
        <template slot="fileSlot" slot-scope="text">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">无文件</span>
          <a-button
            v-else
            :ghost="true"
            type="primary"
            icon="download"
            size="small"
            @click="downloadFile(text)">
            下载
          </a-button>
        </template>

        <span slot="action" slot-scope="text, record">
        <a @click="showModal(record)">审核</a>
                    <a-divider type="vertical" />
          <a @click="showModal(record)">详情</a>
<!--handleDelete
          <a-divider type="vertical" />
          <a-dropdown>
            <a class="ant-dropdown-link">更多 <a-icon type="down" /></a>
            <a-menu slot="overlay">
              <a-menu-item>
                <a @click="handleDetail(record)">详情</a>
              </a-menu-item>
              <a-menu-item>
                <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
                  <a>删除</a>
                </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown>
          -->
        </span>

      </a-table>
    </div>

    <hospitalmonitor-modal ref="modalForm" @ok="modalFormOk"></hospitalmonitor-modal>
  </a-card>
</template>

<script>

  import '@/assets/less/TableExpand.less'
  import { mixinDevice } from '@/utils/mixin'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import HospitalmonitorModal from './modules/HospitalmonitorModal'
  import { axios } from '@/utils/request'
  import { filterObj } from '@/utils/util';
  import moment from "moment";
  import pick from "lodash.pick";

  export default {
    name: 'HospitalmonitorList',
    mixins:[JeecgListMixin, mixinDevice],
    components: {
      HospitalmonitorModal
    },
    data () {
      return {
        dateFlag: false,
        labelCol: { span: 8 },
        wrapperCol: { span: 14 },
        //批量操作记录选中id
        selectIds:[],
        selectObject:[],
        //批量抽查模态框是否显示
        spotCheckModalVisible: false,

        //审核模态框
        visible: false,
        //审核列表
        auditList: [
          {
            title: '序号',
            dataIndex: '',
            key:'rowIndex',
            width:60,
            align:"center",
            customRender:function (t,r,index) {
              return parseInt(index)+1;
            }
          },
          {
            title:'类型',
            align:"center",
            dataIndex: 'type',
            customRender:function (text) {
                if(text=='01'){
                   return '在院';
                }else if(text=='02'){
                   return '上传';
                }else if(text=='03'){
                   return '出院';
                }else if(text=='04'){
                   return '抽查';
                }else if(text=='05'){
                   return '门诊药店';
                }
            }
          },
          {
            title:'所属时间',
            align:"center",
            dataIndex: 'monitdate',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            title:'上传人员',
            align:"center",
            dataIndex: 'hospoperator'
          },
          {
            title:'上传时间',
            align:"center",
            dataIndex: 'hospdate',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            title:'上传状态',
            align:"center",
            dataIndex: 'hospstatus',
            customRender:function (text) {
                if(text==0){
                   return '未上传';
                }else if(text==1){
                   return '自动正常';
                }else if(text==8){
                   return '人工正常';
                }else{
                   return '未上传';
                }
            }
          },
          {
            title:'人脸识别',
            align:"center",
            dataIndex: 'hospitalmonitor.monitorstatus',
            customRender:function (text) {
                if(text==0){
                   return '未通过';
                }else if(text==1){
                   return '自动通过';
                }else if(text==7){
                   return '首次登记';
                }else if(text==8){
                   return '人工通过';
                }else if(text==9){
                  return '未通过';
               }
            }
          },
          {
            title:'识别原因',
            align:"center",
            dataIndex: 'hospitalmonitor.lifestatus',
            customRender:function (text) {
                if(text==1){
                   return '通过识别:系统验证成功';
                }else if(text==2){
                   return '比对失败:原始人脸与认证人脸特征不符';
                }else if(text==3){
                   return '活体验证失败:系统验证认证人脸为非活体';
                }else if(text==4){
                   return '未检测到人脸:系统未检测到人脸';
                }else if(text==7){
                  return '首次验证:本次为数据采集不进行验证';
               }
            }
          },
          {
            title:'定位状态',
            align:"center",
            dataIndex: 'hospitalmonitor.gpscheckstatus',
            customRender:function (text) {
                if(text==0){
                   return '自动正常';
                }else if(text==1){
                   return '偏离';
                }else if(text==8){
                   return '人工正常';
                }else if(text==9){
                   return '偏离';
                }
            }
          },
          {
            title:'操作人员',
            align:"center",
            dataIndex: 'hospitalmonitor.operator'
          },
          {
            title:'操作时间',
            align:"center",
            dataIndex: 'hospitalmonitor.modifydate',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            title:'备注',
            align:"center",
            dataIndex: 'hospitalmonitor.remark'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            fixed:"right",
            width:100,
            scopedSlots: { customRender: 'action' }
          }
        ],


        description: '医院患者服务表管理页面',
        // 表头
        columns: [
          {            dataIndex: 'type'
          },
          {            dataIndex: 'status'
          },
          {
            title: '序号',
            dataIndex: '',
            key:'rowIndex',
            width:60,
            align:"center",
            customRender:function (t,r,index) {
              return parseInt(index)+1;
            }
          },
          {
            title:'姓名',
            align:"center",
            dataIndex: 'name'
          },
          {
            title:'性别',
            align:"center",
            dataIndex: 'sex',
            customRender:function (text) {
               return text ==1?'男':'女';
            }
          },
          {
            title:'身份证号',
            align:"center",
            dataIndex: 'percode'
          },
          {
            title:'住院类型',
            align:"center",
            dataIndex: 'outstatus',
            customRender:function (text) {
                return text ==1?'在院':'出院';
            }
          },
          {
            title:'医院名称',
            align:"center",
            dataIndex: 'hospitalName'
          },
          {
            title:'就诊科室',
            align:"center",
            dataIndex: 'dictName'
          },
          {
            title:'入院时间',
            align:"center",
            dataIndex: 'indate',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            title:'住院编号',
            align:"center",
            dataIndex: 'incode'
          },
          {
            title:'住院诊断',
            align:"center",
            dataIndex: 'diagnose'
          },
          {
            title:'出院日期',
            align:"center",
            dataIndex: 'outdate',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            title:'在院天数',
            align:"center",
            dataIndex: 'lengthOfStay'
          },
          {
            title:'未上传次数',
            align:"center",
            dataIndex: 'hospnum'
          },
          {
            title:'人脸识别',
            align:"center",
            dataIndex: 'monitorstatus',
            customRender:function (text) {
              if(text =='0'){
                  return '未通过';
              }else if(text =='1'){
                  return '自动通过';
              }else if(text =='7'){
                  return '首次登记';
              }else if(text =='8'){
                  return '人工通过';
              }else{
                  return '未通过';
              }
            }
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            fixed:"right",
            width:147,
            scopedSlots: { customRender: 'action' }
          }
        ],
        url: {
          list: "/hospital/monitor/list",
          delete: "/hospital/monitor/delete",
          deleteBatch: "/hospital/monitor/deleteBatch",
          exportXlsUrl: "/hospital/monitor/exportXls",
          importExcelUrl: "hospital/monitor/importExcel",
          auditList: "/monitors/list",
          dictList: "/hospital/dictionary/list",

        },
        dictOptions:[],
        dataSources:[],
        ipaginations:{
          current: 1,
          pageSize: 10,
          pageSizeOptions: ['10', '20', '30'],
          showTotal: (total, range) => {
            return range[0] + "-" + range[1] + " 共" + total + "条"
          },
          showQuickJumper: true,
          showSizeChanger: true,
          total: 0
        },
        //审核->审核->审核
        modal1Visible: false,

        spotCheckForm: {
        },
      }
    },
    created() {

    //xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    this.axios.get("/hospital/dictionary/depart/list/page?token=c41dc2449910ddfb1184e3f7c2ba57de").then(res=>{
                console.log("xxxxxxxxxxxxxxxxxxxxxxxxxxx");
            console.log(res);
    })

    this.url.list="/hospital/monitor/list"
      this.axios.get(this.url.dictList).then(res=>{
            this.dictOptions = res.result;
      })

    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      },
    },
    methods: {
    getQueryParams(){
      //高级查询器
      let sqp = {}
      if(this.superQueryParams){
        sqp['superQueryParams']=encodeURI(this.superQueryParams)
        sqp['superQueryMatchType'] = this.superQueryMatchType
      }
      var param = Object.assign(sqp, this.queryParam, this.isorter ,this.filters);
      param.field = this.getQueryField();
      param.pageNo = this.ipagination.current;
      param.pageSize = this.ipagination.pageSize;
      param.indate = "";
      param.outdate = "";
      return filterObj(param);
    },
      initDictConfig(){
      },

      //模态框
    showModal(record) {
      //替换url
       axios.get(this.url.auditList+'?id='+record.id).then((res)=>{
        console.log(res);

        this.dataSources = res.result.records;
        this.ipagination.total = res.result.total;
       });
      this.visible = true;
    },
      onOutDateChange: function (value, dateString) {
        console.log(dateString[0],dateString[1]);
        this.queryParam.outDateBegin=dateString[0];
        this.queryParam.outDateEnd=dateString[1];
      },
      onInDateChange: function (value, dateString) {
        console.log(dateString[0],dateString[1]);
        this.queryParam.inDateBegin=dateString[0];
        this.queryParam.inDateEnd=dateString[1];
      },
      handleOk(e) {
        this.visible = false;
      },
      handleCancel(e) {
        console.log(e);
        this.visible = false;
      },
      //审核->审核->审核
      setModal1Visible(modal1Visible) {
        this.modal1Visible = modal1Visible;
      },
      //批量抽查模态框
      setSpotCheckModalVisible(spotCheckModalVisible) {
        this.spotCheckModalVisible = spotCheckModalVisible;
      },
      error(message) {
        this.$message.error(message);
      },
      success(message) {
        this.$message.success(message);
      },
      warning(message) {
        this.$message.warning(message);
      },
      //批量抽查
      startSpotCheck(){

        let data = {"hospitalmonitors":this.selectObject,"hmIds":this.selectIds,"checkStatus":this.spotCheckForm.checkStatus,"dateInterval":this.spotCheckForm.dateInterval,"dateStart":this.spotCheckForm.dateStart.format('YYYY-MM-DD HH:mm:ss')};
        console.log("测hi是测试ssssssssssssssssssssssssssssssssssssthis.columns.status"+this.selectionRows);
        this.axios.post("/hospital/spotCheckTask/check",data).then(res=>{
          console.log(res);
        })

        this.spotCheckModalVisible = false;

      },
      //抽查校验
      handleSubmit (e) {
        e.preventDefault()
        this.form.validateFields((err, values) => {
          if (!err) {
            this.$notification['error']({
              message: 'Received values of form:',
              description: values
            })
          }
        })
      },

      handleSpotCheck(id,selectObject) {
        this.selectIds =id;
        if(this.selectIds==''){
          this.warning("请先选择一条抽查的数据!");
        }else{
          this.selectObject = selectObject;
          this.spotCheckModalVisible=true;
        }
      },
      showDateFlag(){
        this.dateFlag=true;
      },
      hiddenDateFlag(){
        this.dateFlag=false;
      },
    },
    // 按照条件置灰勾选框
    computed: {
      rowSelection() {
        return {
          onChange: (selectedRowKeys, selectedRows) => {
            this.selectedRowKeys = selectedRowKeys;
            this.selectionRows = selectedRows;

          },
          getCheckboxProps: record => ({
            props: {
              //01:住院登记 02:行为认证 03:出院 04:抽查
              disabled: record.status=='1' && record.type == '01', // Column configuration not to be checked
            },
          }),
        };
      },
    },

  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>

