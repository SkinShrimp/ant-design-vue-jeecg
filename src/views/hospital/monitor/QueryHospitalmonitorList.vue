<template>
  <a-card :bordered="false">





    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">

<!--          <a-col :md="6" :sm="12">-->
<!--            <a-form-item label="身份证号">-->
<!--                <a-input placeholder="请输入身份证号查询" v-model="queryParam.percode"></a-input>-->
<!--            </a-form-item>-->
<!--          </a-col>-->

<!--          <a-col :md="6" :sm="12">-->
<!--            <a-form-item label="患者姓名">-->
<!--                <a-input placeholder="请输入患者姓名查询" v-model="queryParam.name"></a-input>-->
<!--            </a-form-item>-->
<!--          </a-col>-->

          <a-col :md="6" :sm="8">
            <a-form-item label="医院名称">
              <a-input placeholder="请输入医院名称查询" v-model="queryParam.hospitalName"></a-input>
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
            <a-form-item label="抽查状态">
              <a-select v-model="queryParam.extractstatus" placeholder="请选择住院类型">
                <a-select-option value="1">正在抽查</a-select-option>
                <a-select-option value="0">已经抽查</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>

          <a-col :md="6" :sm="8">
            <a-form-item label="住院类型">
              <a-select v-model="queryParam.outstatus" placeholder="请选择住院类型">
                <a-select-option value="1">在院</a-select-option>
                <a-select-option value="2">出院</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>

          <a-col :md="6" :sm="8">
            <a-form-item label="住院时间">
              <a-range-picker v-model="queryParam.indate"
                              format="YYYY-MM-DD"
                              :placeholder="['开始时间', '结束时间']"
                              @change="onInDateChange" />
            </a-form-item>
          </a-col>
           <a-col :xl="6" :lg="7" :md="8" :sm="24">
                        <a-form-item label="抽查时间">
                          <a-range-picker v-model="queryParam.spoCheckDate"
                                          format="YYYY-MM-DD"
                                          :placeholder="['开始时间', '结束时间']"
                                          @change="spoCheckDateChange" />
                        </a-form-item>

            </a-col>

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



<!--          </template>-->

          <a-col :md="6" :sm="8">
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>&nbsp;&nbsp;
               <a-button type="primary" icon="download" @click="handleExportXls('医院患者服务表')">导出</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>


              <!--&lt;!&ndash;              <a @click="handleToggleSearch" style="margin-left: 8px">&ndash;&gt;-->
<!--&lt;!&ndash;                {{ toggleSearchStatus ? '收起' : '展开' }}&ndash;&gt;-->
<!--&lt;!&ndash;                <a-icon :type="toggleSearchStatus ? 'up' : 'down'"/>&ndash;&gt;-->
<!--&lt;!&ndash;              </a>&ndash;&gt;-->
            </span>
          </a-col>

        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->

    <!-- 操作按钮区域 -->
    <div class="table-operator">
<!--      <a-button type="primary" icon="download" @click="handleExportXls('医院患者服务表')">导出</a-button>-->
<!--      <a-button type="primary" key="1" icon="file-sync" @click="handleSpotCheck(selectedRowKeys,selectionRows)">批量抽查</a-button>-->

    </div>

    <!-- table区域-begin -->
    <div>
<!--      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">-->
<!--        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项-->
<!--        <a style="margin-left: 24px" @click="onClearSelected">清空</a>-->
<!--      </div>-->

      <a-table
        ref="table"
        size="middle"
        :scroll="{x:true}"
        bordered
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"
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

  </a-card>
</template>

<script>

  import '@/assets/less/TableExpand.less'
  import { mixinDevice } from '@/utils/mixin'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
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
    name: 'QueryHospitalmonitorList',
    mixins:[JeecgListMixin, mixinDevice],
    components: {
      PageLayout,
      DetailList,
      DetailListItem
    },
    data () {
      return {
        components: { JDictSelectTag },
        detail:false,
        selectIds:[],
        selectObject:[],
        //批量抽查模态框是否显示
        spotCheckModalVisible: false,

        //审核模态框
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
            title:'住院类型',
            align:"center",
            dataIndex: 'outstatus',
            sorter: true
          },
          {
            title:'抽查状态',
            align:"center",
            dataIndex: 'extractstatus',
            sorter: true
          },
          {
            title:'姓名',
            align:"center",
            dataIndex: 'name',
            sorter: true
          },
          {
            title:'性别',
            align:"center",
            dataIndex: 'sex',
            sorter: true
          },
          {
            title:'身份证号',
            align:"center",
            dataIndex: 'percode',
            sorter: true
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
            title:'入院日期',
            align:"center",
            dataIndex: 'indate',
            sorter: true
          },
          {
            title:'抽查时间',
            align:"center",
            dataIndex: 'numUpdateTime',
          },
          {
            title:'抽查次数',
            align:"center",
            dataIndex: 'checkNum'
          },
          {
            title:'未上传次数',
            align:"center",
            dataIndex: 'notUploadedNum'
          },
          {
            title:'不通过次数',
            align:"center",
            dataIndex: 'notPassNum'
          },
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
          list: "/hospital/spotCheckTask/list",
          delete: "/hospital/monitor/delete",
          deleteBatch: "/hospital/monitor/deleteBatch",
          exportXlsUrl: "/hospital/spotCheckTask/exportXls",
          importExcelUrl: "hospital/monitor/importExcel",
          auditList: "/monitors/list",
          dictList: "/hospital/dictionary/list",

        },
        dictOptions:[],
        dataSources:[],
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

      }
    },
    created() {

    //xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    this.axios.get("/hospital/dictionary/depart/list/page?token=c41dc2449910ddfb1184e3f7c2ba57de").then(res=>{
                console.log("xxxxxxxxxxxxxxxxxxxxxxxxxxx");
            console.log(res);
    })

    this.url.list="/hospital/spotCheckTask/list"
      this.axios.get(this.url.dictList).then(res=>{
            this.dictOptions = res.result;
      })

    },
    methods: {
      getQueryParams() {
        //高级查询器
        let sqp = {}
        if (this.superQueryParams) {
          sqp['superQueryParams'] = encodeURI(this.superQueryParams)
          sqp['superQueryMatchType'] = this.superQueryMatchType
        }
        var param = Object.assign(sqp, this.queryParam, this.isorter, this.filters);
        param.field = this.getQueryField();
        param.pageNo = this.ipagination.current;
        param.pageSize = this.ipagination.pageSize;
        param.indate = "";
        param.outdate = "";
        param.spoCheckDate="";
        return filterObj(param);
      },
      initDictConfig() {
      },

      handleOk(e) {
        this.visible = false;
      },
      handleCancel(e) {
        console.log(e);
        this.visible = false;
      },
      error(message) {
        this.$message.error(message);
      },
      success(message) {
        this.$message.success(message);
      },
      spoCheckDateChange: function (value, dateString) {
        console.log(dateString[0],dateString[1]);
        this.queryParam.spoCheckDateBegin=dateString[0];
        this.queryParam.spoCheckDateEnd=dateString[1];
      },
      onInDateChange: function (value, dateString) {
        console.log(dateString[0],dateString[1]);
        this.queryParam.inDateBegin=dateString[0];
        this.queryParam.inDateEnd=dateString[1];
      },
      warning(message) {
        this.$message.warning(message);
      },
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>

