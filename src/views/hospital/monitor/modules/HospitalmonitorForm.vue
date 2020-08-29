<template>
  <a-modal
    :title="title"
    :width="1200"
    :visible="visible"
    :maskClosable="false"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    @cancel="handleCancel">

    <a-spin :spinning="confirmLoading">

    </a-spin>
  </a-modal>
</template>

<script>

  import '@/assets/less/TableExpand.less'
  import { mixinDevice } from '@/utils/mixin'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
//  import HospitalmonitorModal from './modules/HospitalmonitorModal'

  export default {
    name: 'HospitalmonitorForm',
    components: {
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


  	//弹出框
  function createwindowoktext(title, addurl, width, height, oktext, canceltext) {
  	width = width ? width : 700;
  	height = height ? height : 400;
  	if (width == "100%" || height == "100%") {
  		width = window.top.document.body.offsetWidth;
  		height = window.top.document.body.offsetHeight - 100;
  	}
  	//--author：JueYue---------date：20140427---------for：弹出bug修改,设置了zindex()函数
  	if (typeof (windowapi) == 'undefined') {
  		$.dialog({
  			content : 'url:' + addurl,
  			lock : true,
  			width : width,
  			//zIndex:1990,
  			height : height,
  			parent : windowapi,
  			title : title,
  			opacity : 0.3,
  			cache : false,
  			/* ok : function() {		//去掉确定的方法
  				iframe = this.iframe.contentWindow;
  				saveObj();
  				return false;
  			}, */
  			 button:[{				//自定义设置按钮
              name: '保存',
              callback: function(){
                  iframe = this.iframe.contentWindow;//获取弹出层的iframe
                  saveParam();//自定义保存数据方法
                  return false;//阻止页面关闭（默认为true不关闭）
              }
          },{//设置按钮
              name: '关闭',
              callback: function(){
                  iframe = this.iframe.contentWindow;//获取弹出层的iframe
                  closeButton();//调用关闭弹出框窗口方法
                  return false;//阻止页面关闭（默认为true不关闭）
              }
          }],
  		//	okVal : oktext,//去掉确定按钮
  			cancelVal : canceltext,
  			cancel : false /*为true等价于function(){}*/
  		}).zindex();
  	} else {
  		W.$.dialog({
  			content : 'url:' + addurl,
  			lock : true,
  			width : width,
  			//zIndex:1990,
  			height : height,
  			parent : windowapi,
  			title : title,
  			opacity : 0.3,
  			cache : false,
  			/* ok : function() {//去掉确定的方法
  				iframe = this.iframe.contentWindow;
  				saveObj();
  				return false;
  			}, */
  			 button:[{//设置按钮
              name: '保存',
              callback: function(){
                  iframe = this.iframe.contentWindow;//获取弹出层的iframe
                  saveParam();//自定义保存数据方法
                  return false;//阻止页面关闭（默认为true不关闭）
              }
          },{//设置按钮
              name: '关闭',
              callback: function(){
                  iframe = this.iframe.contentWindow;//获取弹出层的iframe
                  closeButton();		//调用关闭弹出框窗口方法
                  return false;		//阻止页面关闭（默认为true不关闭）
              }
          }],
  		//	okVal : oktext,			//去掉确定按钮
  			cancelVal : canceltext,
  			cancel : false /*为true等价于function(){}*/
  		}).zindex();
  	}
  	//--author：wjl---------date：20170206---------for：弹出bug修改,设置了文本函数
  }


    	 	//点击关闭事件
     function closeButton(){
    			frameElement.api.opener.location.reload();//关闭重新加载一次
    			//frameElement.api.close(); //关闭弹出窗口
    		}



    	function saveParam(){
    	alert(111)
    	}
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>
