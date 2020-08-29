<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <a-form :form="form" slot="detail">
        <a-row>
          <a-col :span="24">
            <a-form-item label="hmid" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input-number v-decorator="['hmid']" placeholder="请输入hmid" style="width: 100%"/>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="type" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['type']" placeholder="请输入type"></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="monitdate" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-date placeholder="请选择monitdate" v-decorator="['monitdate']" :trigger-change="true" style="width: 100%"/>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="hospdate" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-date placeholder="请选择hospdate" v-decorator="['hospdate']" :trigger-change="true" style="width: 100%"/>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="hospstatus" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['hospstatus']" placeholder="请输入hospstatus"></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="hospoperator" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['hospoperator']" placeholder="请输入hospoperator"></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="inid" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input-number v-decorator="['inid']" placeholder="请输入inid" style="width: 100%"/>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="status" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['status']" placeholder="请输入status"></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="operator" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['operator']" placeholder="请输入operator"></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="modifydate" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-date placeholder="请选择modifydate" v-decorator="['modifydate']" :trigger-change="true" style="width: 100%"/>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="remark" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['remark']" placeholder="请输入remark"></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="checkstatus" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['checkstatus']" placeholder="请输入checkstatus"></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="checkdate" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-date placeholder="请选择checkdate" v-decorator="['checkdate']" :trigger-change="true" style="width: 100%"/>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="checkoperator" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['checkoperator']" placeholder="请输入checkoperator"></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="checkreason" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['checkreason']" placeholder="请输入checkreason"></a-input>
            </a-form-item>
          </a-col>
          <a-col v-if="showFlowSubmitButton" :span="24" style="text-align: center">
            <a-button @click="submitForm">提 交</a-button>
          </a-col>
        </a-row>
      </a-form>
    </j-form-container>
  </a-spin>
</template>

<script>

  import { httpAction, getAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import { validateDuplicateValue } from '@/utils/util'
  import JFormContainer from '@/components/jeecg/JFormContainer'
  import JDate from '@/components/jeecg/JDate'  

  export default {
    name: 'MonitorListForm',
    components: {
      JFormContainer,
      JDate,
    },
    props: {
      //流程表单data
      formData: {
        type: Object,
        default: ()=>{},
        required: false
      },
      //表单模式：true流程表单 false普通表单
      formBpm: {
        type: Boolean,
        default: false,
        required: false
      },
      //表单禁用
      disabled: {
        type: Boolean,
        default: false,
        required: false
      }
    },
    data () {
      return {
        form: this.$form.createForm(this),
        model: {},
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },
        confirmLoading: false,
        validatorRules: {
        },
        url: {
          add: "/monitors/monitorList/add",
          edit: "/monitors/monitorList/edit",
          queryById: "/monitors/monitorList/queryById"
        }
      }
    },
    computed: {
      formDisabled(){
        if(this.formBpm===true){
          if(this.formData.disabled===false){
            return false
          }
          return true
        }
        return this.disabled
      },
      showFlowSubmitButton(){
        if(this.formBpm===true){
          if(this.formData.disabled===false){
            return true
          }
        }
        return false
      }
    },
    created () {
      //如果是流程中表单，则需要加载流程表单data
      this.showFlowData();
    },
    methods: {
      add () {
        this.edit({});
      },
      edit (record) {
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'hmid','type','monitdate','hospdate','hospstatus','hospoperator','inid','status','operator','modifydate','remark','checkstatus','checkdate','checkoperator','checkreason'))
        })
      },
      //渲染流程表单数据
      showFlowData(){
        if(this.formBpm === true){
          let params = {id:this.formData.dataId};
          getAction(this.url.queryById,params).then((res)=>{
            if(res.success){
              this.edit (res.result);
            }
          });
        }
      },
      submitForm () {
        const that = this;
        // 触发表单验证
        this.form.validateFields((err, values) => {
          if (!err) {
            that.confirmLoading = true;
            let httpurl = '';
            let method = '';
            if(!this.model.id){
              httpurl+=this.url.add;
              method = 'post';
            }else{
              httpurl+=this.url.edit;
               method = 'put';
            }
            let formData = Object.assign(this.model, values);
            console.log("表单提交数据",formData)
            httpAction(httpurl,formData,method).then((res)=>{
              if(res.success){
                that.$message.success(res.message);
                that.$emit('ok');
              }else{
                that.$message.warning(res.message);
              }
            }).finally(() => {
              that.confirmLoading = false;
            })
          }
         
        })
      },
      popupCallback(row){
        this.form.setFieldsValue(pick(row,'hmid','type','monitdate','hospdate','hospstatus','hospoperator','inid','status','operator','modifydate','remark','checkstatus','checkdate','checkoperator','checkreason'))
      },
    }
  }
</script>