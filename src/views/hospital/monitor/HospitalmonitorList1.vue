<template>
  <a-card :bordered="false">

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
              <a-select v-model="queryParam.outstatus" placeholder="请选类型">
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
                                          @change="onBirthdayChange" />
                        </a-form-item>
            </a-col>

            <a-col :md="6" :sm="8">
              <a-form-item label="医院名称">
                <a-input placeholder="请输入医院名称查询" v-model="queryParam.hisname"></a-input>
              </a-form-item>
            </a-col>

            <a-col :md="6" :sm="8">
              <a-form-item label="就诊科室">
                <a-input placeholder="请输入就诊科室查询" v-model="queryParam.dept"></a-input>
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
                                            @change="onBirthdayChange" />
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
      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-button type="primary" icon="download" @click="handleExportXls('医院患者服务表')">导出</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">
        <a-button type="primary" icon="import">导入</a-button>
      </a-upload>
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>
      </a-dropdown>
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
        :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
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
          <a @click="handleEdit(record)">审核</a>
                    <a-divider type="vertical" />
          <a @click="handleEdit(record)">抽查</a>
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

  export default {
    name: 'HospitalmonitorList',
    mixins:[JeecgListMixin, mixinDevice],
    components: {
      HospitalmonitorModal
    },
    data () {
      return {
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
            dataIndex: 'hospital.name'
          },
          {
            title:'就诊科室',
            align:"center",
            dataIndex: 'dicDept.name'
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
            dataIndex: ''
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
          
        },
        dictOptions:{},
      }
    },
    created() {
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      },
    },
    methods: {
      initDictConfig(){
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>