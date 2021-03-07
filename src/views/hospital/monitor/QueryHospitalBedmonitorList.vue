<template>
  <a-card :bordered="false">





    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">

          <a-col :md="6" :sm="8">
            <a-form-item label="登录用户名称">
              <a-input placeholder="请输入登录用户名称查询" v-model="queryParam.username"></a-input>
            </a-form-item>
          </a-col>

          <a-col :md="6" :sm="8">
            <a-form-item label="登录用户密码">
              <a-input type="password" placeholder="请输入登录用户密码查询" v-model="queryParam.password"></a-input>
            </a-form-item>
          </a-col>

          <a-col :md="6" :sm="8">
            <a-form-item label="医院名称">
              <a-input placeholder="请输入医院名称查询" v-model="queryParam.hospitalName"></a-input>
            </a-form-item>
          </a-col>

          <a-col :md="6" :sm="8">
            <a-form-item label="医院编号">
              <a-input placeholder="请输入医院名称查询" v-model="queryParam.hospitalCode"></a-input>
            </a-form-item>
          </a-col>

          <a-col :md="6" :sm="8">
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>&nbsp;&nbsp;
               <a-button type="primary" icon="download" @click="handleExportXls('医院床位服务表')">导出</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
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
    name: 'QueryHospitalBedmonitorList',
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
            title:'医院名称',
            align:"center",
            dataIndex: 'hospitalName',
            sorter: true
          },
          {
            title:'医院编码',
            align:"center",
            dataIndex: 'hospitalCode',
          },
          {
            title:'科室',
            align:"center",
            dataIndex: 'department',
            sorter: true
          },
          {
            title:'审批床位',
            align:"center",
            dataIndex: 'beds',
          },
          {
            title:'在院人数',
            align:"center",
            dataIndex: 'numPeopleInhos',
          },
          {
            title:'占床率',
            align:"center",
            dataIndex: 'occupationRateStr',
          },
          //   align:"center",
          //   fixed:"right",
          //   width:147,
          //   scopedSlots: { customRender: 'action' }
          // }
        ],
        url: {
          list: "/hospital/monitor/bed/list",
          delete: "/hospital/monitor/delete",
          deleteBatch: "/hospital/monitor/deleteBatch",
          exportXlsUrl: "/hospital/monitor/exportXls2",
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
      warning(message) {
        this.$message.warning(message);
      },
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>

