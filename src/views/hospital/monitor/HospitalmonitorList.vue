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
      @cancel="() => reloadPagination()"

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
            :rowSelection="rowSelection"
            class="j-table-force-nowrap"
            @change="handleTableChange"
                  >
            <span slot="action" slot-scope="text, recordid">
            <a @click="setModal1Visible(recordid)">审核</a>
                        <a-divider type="vertical" />
            <a @click="showDetail(true)">详情</a>

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
      :visible="detail"
      :ok-button-props="{ props: { disabled: true } }"
      :cancel-button-props="{ props: { disabled: true } }"
      @ok="handleOk"
      @cancel="showDetail(false)"
    >
      <template>
        <div style="width: 1100px; height: 500px" >
        <div style="float:left;width: 850px;">
          <a-row  >
            <a-col :span="5">
              <p class="height-100">
                姓名: &nbsp;&nbsp;{{details.name}}
              </p>
            </a-col>
            <a-col :span="4">
              <p class="height-50">
                性别: &nbsp;&nbsp;{{details.sexName}}
              </p>
            </a-col>
            <a-col :span="7">
              <p class="height-120">
                身份证号: &nbsp;&nbsp;{{details.percode}}
              </p>
            </a-col>
            <a-col :span="4">
              <p class="height-80">
                联系电话: &nbsp;&nbsp;{{details.phone}}
              </p>
            </a-col>
          </a-row>
          <a-row >
            <a-col :span="5">
              <p class="height-80">
                社保编号: &nbsp;&nbsp;{{details.inscode}}
              </p>
            </a-col>
              <a-col :span="5">
                <p class="height-80">
                  入院编号: &nbsp;&nbsp;{{details.incode}}
                </p>
              </a-col>
            <a-col :span="5">
              <p class="height-80">
                医院名称: &nbsp;&nbsp;{{details.hisName}}
              </p>
            </a-col>
            <a-col :span="5">
              <p class="height-80">
                入院日期: &nbsp;&nbsp;{{details.indate}}
              </p>
            </a-col>
            </a-row>
          <a-row >
            <a-col :span="5">
              <p class="height-80">
                科室: &nbsp;&nbsp;{{details.deptName}}
              </p>
            </a-col>
            <a-col :span="5">
              <p class="height-80">
                诊断: &nbsp;&nbsp;{{details.diagnose}}
              </p>
            </a-col>
            <a-col :span="5">
              <p class="height-80">
                病房编号: &nbsp;&nbsp;{{details.wardcode}}
              </p>
            </a-col>
            <a-col :span="5">
              <p class="height-80">
                出院日期: &nbsp;&nbsp;{{details.outdate}}
              </p>
            </a-col>
          </a-row>
          <a-row >
            <a-col :span="5">
              <p class="height-80">
                费用编号: &nbsp;&nbsp;{{details.moneycode}}
              </p>
            </a-col>
            <a-col :span="5">
              <p class="height-80">
                费用金额: &nbsp;&nbsp;{{details.money}}
              </p>
            </a-col>
          </a-row>
          <a-row >
            <a-col :span="5">
              <p class="height-80">
                <img :src="details.image1" style="width: 100px; height: 150px " />
              </p>
            </a-col>
            <a-col :span="5">
              <p class="height-80">
                <img :src="details.image2" style="width: 100px; height: 150px " />
              </p>
            </a-col>
            <a-col :span="5">
              <p class="height-80">
                <img :src="details.image3" style="width: 100px; height: 150px " />
              </p>
            </a-col>
            <a-col :span="5">
              <p class="height-80">
                <img :src="details.image4" style="width: 100px; height: 150px " />
              </p>
            </a-col>
          </a-row>
          <a-row >
            <a-col :span="5">
              <p class="height-80">
                <img :src="details.image5" style="width: 100px; height: 150px " />
              </p>
            </a-col>
            <a-col :span="5">
              <p class="height-80">
                <img :src="details.image6" style="width: 100px; height: 150px " />
              </p>
            </a-col>
            <a-col :span="5">
              <p class="height-80">
                <img :src="details.image7" style="width: 100px; height: 150px " />
              </p>
            </a-col>
            <a-col :span="5">
              <p class="height-80">
                <img :src="details.image8" style="width: 100px; height: 150px " />
              </p>
            </a-col>
          </a-row>
        </div>
          <div style="float:left; width: 100px; height: 250px;">
            <img :src="details.image" style="width: 100px; height: 150px " />
          </div>
        </div>
      </template>

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
          <div style="width: 100%;margin-left: -2%;margin-top: 20px">抽查方式&nbsp;:&nbsp;&nbsp;
            <a-radio-group v-model="spotCheckForm.checkStatus" :default-value="1">
              <a-radio :value="1" @click="hiddenDateFlag()" >马上抽查</a-radio>
              <a-radio :value="0" @click="showDateFlag()">定时抽查</a-radio>
            </a-radio-group>
          </div>
      <div style="width: 100%;margin-left: -4%;margin-top: 20px"><font color="red">*</font>&nbsp;抽查时间间隔&nbsp;:&nbsp;&nbsp;
        <j-dict-select-tag v-model="spotCheckForm.dateInterval" style="width: 150px" placeholder="请选择职级" dictCode="spot_check_time"/>
      </div>
        <a-dropdown v-if="dateFlag">
          <div style="width: 100%;margin-left: 2%;margin-top: 20px"><font color="red">*</font>&nbsp;抽查开始时间&nbsp;:&nbsp;&nbsp;
            <a-date-picker
              show-time
              format="YYYY-MM-DD HH:mm:ss"
              placeholder="请选择抽查日期"
              style="width: 150px;"
              v-model="spotCheckForm.dateStart"
            />
          </div>
        </a-dropdown>
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
      @ok="() => review()"
      @cancel="() => setModal1Visible('-1')"
    >

        <div style="width: 100%;margin-left: -2%;margin-top: 20px">上传状态&nbsp;:&nbsp;&nbsp;
              <a-select style="width: 150px" v-model="column.hospstatus" placeholder="请选择上传状态">
                <a-select-option value="8">人工正常</a-select-option>
                <a-select-option value="9">未上传</a-select-option>
              </a-select>
        </div>
      <div style="width: 100%;margin-left: -2%;margin-top: 20px">人脸识别&nbsp;:&nbsp;&nbsp;
              <a-select style="width: 150px" v-model="column.monitorstatus" placeholder="请选择人脸识别">
                <a-select-option value="8">人工通过</a-select-option>
                <a-select-option value="9">未通过</a-select-option>
              </a-select>
      </div>

      <div style="width: 100%;margin-left: -2%;margin-top: 20px">定位状态&nbsp;:&nbsp;&nbsp;
              <a-select style="width: 150px" v-model="column.gpscheckstatus" placeholder="请选择定位状态">
                <a-select-option value="8">人工正常</a-select-option>
                <a-select-option value="9">偏离</a-select-option>
              </a-select>
      </div>

      <div style="width: 100%;margin-left: -2%;margin-top: 20px">备注&nbsp;:&nbsp;&nbsp;
                <a-input style="width: 150px" placeholder="" v-model="column.remark"></a-input>
      </div>

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

          <!--          <a-col :md="6" :sm="8">-->
<!--            <a-form-item label="住院类型">-->
<!--              <a-select v-model="queryParam.outstatus" placeholder="请选择住院类型">-->
<!--                <a-select-option value="1">在院</a-select-option>-->
<!--                <a-select-option value="2">出院</a-select-option>-->
<!--                <a-select-option value="4">抽查</a-select-option>-->
<!--              </a-select>-->
<!--            </a-form-item>-->
<!--          </a-col>-->

<!--          <template v-if="toggleSearchStatus">-->
<!--            <a-col :md="6" :sm="8">-->
<!--              <a-form-item label="住院编号">-->
<!--                <a-input placeholder="请输入住院编号名字" v-model="queryParam.incode"></a-input>-->
<!--              </a-form-item>-->
<!--            </a-col>-->





<!--            <a-col :md="6" :sm="8">-->
<!--              <a-form-item label="住院诊断">-->
<!--                <a-input placeholder="请输入住院诊断查询" v-model="queryParam.diagnose"></a-input>-->
<!--              </a-form-item>-->
<!--            </a-col>-->

<!--             <a-col :xl="6" :lg="7" :md="8" :sm="24">-->
<!--                          <a-form-item label="出院时间">-->
<!--                            <a-range-picker v-model="queryParam.outdate"-->
<!--                                            format="YYYY-MM-DD"-->
<!--                                            :placeholder="['开始时间', '结束时间']"-->
<!--                                            @change="onOutDateChange" />-->
<!--                          </a-form-item>-->

<!--              </a-col>-->

<!--          </template>-->

          <a-col :md="6" :sm="8">
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>&nbsp;&nbsp;
                    <a-button type="primary" key="1" icon="file-sync" @click="handleSpotCheck(selectedRowKeys,selectionRows)">批量抽查</a-button>
              <!--              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>-->
<!--              <a @click="handleToggleSearch" style="margin-left: 8px">-->
<!--                {{ toggleSearchStatus ? '收起' : '展开' }}-->
<!--                <a-icon :type="toggleSearchStatus ? 'up' : 'down'"/>-->
<!--              </a>-->
            </span>
          </a-col>

        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->

    <!-- 操作按钮区域 -->
    <div class="table-operator">
<!--      <a-button type="primary" icon="download" @click="handleExportXls('医院患者服务表')">导出</a-button>-->

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

<!--        <span slot="action" slot-scope="text, record">-->
<!--        <a @click="showModal(record)">审核</a>-->
<!--                    <a-divider type="vertical" />-->
<!--          <a @click="showDetails(record)">详情</a>-->
<!--        </span>-->

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
  import JDictSelectTag from '@/components/dict/JDictSelectTag'
  import {initDictOptions, filterDictText} from '@/components/dict/JDictSelectUtil'

  import {getAction, getFileAccessHttpUrl, httpAction} from "@api/manage";
  import { duplicateCheck } from '@/api/api'


  import PageLayout from '@/components/page/PageLayout'
  import DetailList from '@/components/tools/DetailList'
  const DetailListItem = DetailList.Item

  export default {
    name: 'HospitalmonitorList',
    mixins:[JeecgListMixin, mixinDevice],
    components: {
      HospitalmonitorModal,
      PageLayout,
      DetailList,
      DetailListItem
    },
    data () {
      return {
        details:{

        },
        column:{
        },
        components: { JDictSelectTag },
        detail:false,
        hmid:"",
        id:"",

        dateFlag: false,
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
            title: '备注',
            align: "center",
            dataIndex: 'hospitalmonitor.remark'
          }
          // },
          // {
          //   title: '操作',
          //   dataIndex: 'action',
          //   align:"center",
          //   fixed:"right",
          //   width:100,
          //   scopedSlots: { customRender: 'action' }
          // }
        ],


        description: '医院患者服务表管理页面',
        // 表头
        columns: [
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
            title:'医院名称',
            align:"center",
            dataIndex: 'hospitalName',
            sorter: true
          },
          {
            title:'就诊科室',
            align:"center",
            dataIndex: 'dictName',
            sorter: true
          },
          {
            title:'入院时间',
            align:"center",
            dataIndex: 'indate',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            },
          },
          {
            title:'住院诊断',
            align:"center",
            dataIndex: 'diagnose'
          },
          {
            title:'床位号',
            align:"center",
            dataIndex: 'wardcode',
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
          }
          // {
          //   title: '操作',
          //   dataIndex: 'action',
          //   align:"center",
          //   fixed:"right",
          //   width:147,
          //   scopedSlots: { customRender: 'action' }
          // }
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
          pageSize: 100,
          pageSizeOptions: ['100', '150', '200'],
          showTotal: (total, range) => {
            return range[0] + "-" + range[1] + " 共" + total + "条"
          },
          showQuickJumper: true,
          showSizeChanger: true,
          total: 0
        },
        ipagination:{
          current: 1,
          pageSize: 100,
          pageSizeOptions: ['100', '150', '200'],
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
    // computed: {
    //   importExcelUrl: function(){
    //     return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
    //   },
    // },
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
         this.hmid=record.id;
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
      setModal1Visible(recordid) {
        this.column={};
        if(recordid=='-1'){
          this.modal1Visible = false;
          return ;
        }
        this.id=recordid.id;
        this.modal1Visible = true;
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
      startSpotCheck(e){
        // 触发表单验证
        if(!this.spotCheckForm.dateInterval){
          this.warning("抽查时间间隔不可为空！");
          return ;
        }
        if(this.spotCheckForm.checkStatus==0 && (this.spotCheckForm.dateStart==null || this.spotCheckForm.dateStart=='' || !this.spotCheckForm.dateStart )) {
          this.warning("抽查开始日期不可为空！");
          return ;
        }
        let data =
   {"hospitalmonitors":this.selectObject,"hmIds":this.selectIds,"checkStatus":this.spotCheckForm.checkStatus,"dateInterval":this.spotCheckForm.dateInterval,"dateStart":moment(this.spotCheckForm.dateStart).format('YYYY-MM-DD HH:mm:ss')};
        this.axios.post("/hospital/spotCheckTask/check",data).then(res=>{
          console.log(res);
          var params = this.getQueryParams();//查询条件
          getAction(this.url.list, params).then((res) => {
            if (res.success) {
              this.dataSource = res.result.records;
              this.ipagination.total = res.result.total;
            }
          })
        })

        this.spotCheckModalVisible = false;

      },
      // 审核
      review(){
        if(!this.column.gpscheckstatus && !this.column.monitorstatus && !this.column.hospstatus && !this.column.remark){
          this.warning("参数全部为空！");
          return ;
        }
        let data =   {"hmid":this.hmid,"id":this.id,"gpscheckstatus":this.column.gpscheckstatus,"monitorstatus":this.column.monitorstatus,"hospstatus":this.column.hospstatus,"remark":this.column.remark};
        this.axios.post("/monitors/update",data).then(res=>{
          console.log(res);
        })
        this.modal1Visible = false;
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

      showDetail(flag){
        axios.get("/hospital/monitor/detail?id="+this.hmid).then((res)=>{
          this.details = res;
        });
        this.detail=flag;
      },
      showDetails(record){
        axios.get("/hospital/monitor/detail?id="+record.id).then((res)=>{
          this.details = res;
        });
        this.detail=true;
      },
      hiddenDateFlag(){
        this.dateFlag=false;
      },
      reloadPagination(e){

        axios.get(this.url.list+"?startIndex=0&pageSize=10").then((res)=>{
          console.log(res);

          this.dataSources = res.result.records;
          this.ipagination.total = res.result.total;
        });
        console.log("ssssssssssssssssssssss");
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
              disabled: record.outstatus!='1', // Column configuration not to be checked
            },
          }),
        };
      },
    },
    validate (rule, value, callback) {
      const regex = /^user-(.*)$/
      if (!regex.test(value)) {
        callback('需要以 user- 开头')
      }advanced
      callback()
    }

  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>

<style lang="less" scoped>
.page-header-wrapper-grid-content-main {
  width: 100%;
  height: 100%;
  min-height: 100%;
  transition: .3s;

  .account-center-avatarHolder {
    text-align: center;
    margin-bottom: 24px;


    .username {
      color: rgba(0, 0, 0, 0.85);
      font-size: 20px;
      line-height: 28px;
      font-weight: 500;
      margin-bottom: 4px;
    }
  }

  .account-center-detail {

    p {
      margin-bottom: 8px;
      padding-left: 26px;
      position: relative;
    }

    i {
      position: absolute;
      height: 14px;
      width: 14px;
      left: 0;
      top: 4px;
      background: url(https://gw.alipayobjects.com/zos/rmsportal/pBjWzVAHnOOtAUvZmZfy.svg)
    }

    .title {
      background-position: 0 0;
    }
    .group {
      background-position: 0 -22px;
    }
    .address {
      background-position: 0 -44px;
    }
  }

  .account-center-tags {
    .ant-tag {
      margin-bottom: 8px;
    }
  }

  .account-center-team {

    .members {
      a {
        display: block;
        margin: 12px 0;
        line-height: 24px;
        height: 24px;
        .member {
          font-size: 14px;
          color: rgba(0, 0, 0, .65);
          line-height: 24px;
          max-width: 100px;
          vertical-align: top;
          margin-left: 12px;
          transition: all 0.3s;
          display: inline-block;
        }
        &:hover {
          span {
            color: #1890ff;
          }
        }
      }
    }
  }

  .tagsTitle, .teamTitle {
    font-weight: 500;
    color: rgba(0,0,0,.85);
    margin-bottom: 12px;
  }

}

</style>