<template>
  <DialogForm ref="dialogForm">
    <template v-slot:header>
      <h4 class="modal-title" v-if="insertMode">{{ labels.title_new }}</h4>
      <h4 class="modal-title" v-if="updateMode">{{ labels.title_edit }}</h4>
    </template>
    <template v-slot:default>
        <div class="row row-height">
          <div class="col-height col-md-7">
            <label for="taskid">{{ labels.taskid_label }}</label>
            <div class="input-group has-validation" :class="{'has-error': v$.taskid.$error}">
              <input ref="taskid" type="text" v-model="localData.taskid" id="taskid" class="form-control input-md" maxlength="50" readonly />
              <label class="required">*</label>
            </div>
            <span v-if="v$.taskid.$error" class="has-error">{{ v$.taskid.$errors[0].$message }}</span>
          </div>
          <div class="col-height col-md-3">
            <div class="checkbox checkbox form-check" id="shareflagbox">									
              <input ref="shareflag" type="checkbox" id="shareflag" :true-value="1" :false-value="0" v-model="localData.shareflag" class="form-control input-md form-check-input" title="Sharing task with other" />
              <label for="shareflag" class="control-label form-check-label" title="Sharing task with other">{{ labels.shareflag_label }}</label>
            </div>
          </div>
        </div>
        <div class="row row-height">
          <div class="col-height col-md-12">
            <label for="taskname">{{ labels.taskname_label }}</label>
            <div class="input-group has-validation" :class="{'has-error': v$.taskname.$error}">
              <input ref="taskname" type="text" v-model="localData.taskname" id="taskname" class="form-control input-md" maxlength="150" /> 
              <label class="required">*</label>
            </div>
            <span v-if="v$.taskname.$error" class="has-error">{{ v$.taskname.$errors[0].$message }}</span>
          </div>
        </div>
        <div class="row row-height">
          <div class="col-height col-md-7">
            <label>{{ labels.connectid_label }}</label>
            <div class="input-group" :class="{'has-error': v$.connectid.$error}">
              <select ref="connectid" v-model="localData.connectid" class="form-control input-md">
                <option v-for="(value,key) in dataCategory.tmigrateconnect" :key="key" :value="key">{{value}}</option>
              </select>
              <a href="javascript:void(0)" id="connecteditbutton" class="input-group-addon input-group-append input-group-text" title="Edit Connection" tabindex="-1"><em class="fa fa-edit" aria-hidden="true"></em></a>
              <a href="javascript:void(0)" id="connectnewbutton" class="input-group-addon input-group-append input-group-text" title="New Connection" tabindex="-1"><em class="fa fa-plus" aria-hidden="true"></em></a>
            </div>
            <span v-if="v$.connectid.$error" class="has-error">{{ v$.connectid.$errors[0].$message }}</span>
          </div>
        </div>
        <div class="row row-height">
          <div class="col-height col-md-12">
            <label for="taskconfigs">{{labels.taskconfigs_label}}</label>
            <div class="input-group has-validation" :class="{'has-error': v$.taskconfigs.$error}">
              <textarea ref="taskconfigs" type="text" v-model="localData.taskconfigs" id="taskconfigs" class="form-control input-md validate-json" rows="5"></textarea>
            </div>
            <span v-if="v$.taskconfigs.$error" class="has-error">{{ v$.taskconfigs.$errors[0].$message }}</span>
          </div>
        </div>
        <div class="row row-height">
          <div class="col-height col-md-12">
            <label>{{ labels.taskmodel_label }}</label>
            <a href="javascript:void(0)" id="addmodelbutton" class="btn btn-primary btn-md" title="Add Model" tabindex="-1"><i class="fa fa-plus fa-btn-icon" aria-hidden="true"></i></a>
          </div>
        </div>
        <div id="fsmodeltablayer">
          <ul id="fsmodeltabbar" class="nav nav-tabs navbar-left nav-tabbar-agent" role="tablist">
            <template v-for="(item,index) in localData.models" :key="item.modelid">
              <li class="nav-item active" role="presentation">
                <a class="nav-link model-link" data-toggle="tab" role="tab"
                  :id="'linker_' + item.modelid" 
                  :class="{ active: index === 0 }"
                  :href="'#entrymodel_' + item.modelid"
                  :data-target="'#entrymodel_' + item.modelid"                            
                  :aria-controls="'entrymodel_' + item.modelid"
                  :aria-selected="index === 0 ? 'true' : 'false'"
                >
                  <label>{{ item.modelname }}</label>
                  <span v-if="!(index === 0)" :id="'dellink_' + item.modelid" class="del-linker">
                    <i class="fa fa-times-circle"></i>
                  </span>
                </a>
              </li>
            </template>
          </ul>
        </div>
        <div id="entrytabmodel" class="tab-content">
          <template v-for="(item,index) in localData.models" :key="item.modelid">
            <div class="tab-pane fade entry-models" :class="{ 'active show': index === 0 }" :id="'entrymodel_'+item.modelid" :data-model="item.modelid">
              <div class="row row-height">
                  <div class="col-height col-md-10">
<label class="control-label">{{ labels.modelname_label }}</label>
<input type="text" v-model="item.modelid" :id="'modelname_' + item.modelid" class="form-control input-md irequired alert-input model-name" maxlength="150" :data-model="item.modelid"/>
                  </div>
              </div>
              <div class="row row-height">
                <div class="col-height col-md-5">
<label class="control-label">{{ labels.tablename_label }}</label>
<input type="text" v-model="item.tablename" :id="'tablename_' + item.modelid" class="form-control input-md irequired alert-input table-name" maxlength="50" />
                </div>
              </div>
              <div class="row row-height">
                <div class="col-height col-md-12">
<label class="control-label">{{ labels.tablefields_label }}</label>
<textarea v-model="item.tablefields" :id="'tablefields_' + item.modelid" class="form-control input-md irequired alert-input table-fields validate-json" rows="15" />
                </div>
              </div>
              <div class="row row-height">
                <div class="col-height col-md-12">
<label class="control-label">{{ labels.tablesettings_label }}</label>
<textarea v-model="item.tablesettings" :id="'tablesettings_' + item.modelid" class="form-control input-md irequired alert-input table-settings validate-json" rows="5" />
                </div>
              </div>
            </div>
          </template>
        </div>
    </template>
    <template v-slot:footer>
      <button ref="savebutton" id="savebutton" class="btn btn-dark btn-sm" @click="saveClick" v-if="insertMode"><em class="fa fa-save fa-btn-icon"></em>{{ labels.save_button }}</button>
      <button ref="updatebutton" id="updatebutton" class="btn btn-dark btn-sm" @click="updateClick" v-if="updateMode"><em class="fa fa-save fa-btn-icon"></em>{{ labels.update_button }}</button>
      <button class="btn btn-dark btn-sm" data-dismiss="modal"><em class="fa fa-close fa-btn-icon"></em>{{ labels.cancel_button }}</button>
    </template>
  </DialogForm>
</template>
<script>
import { ref, computed, watch } from 'vue';
import { useVuelidate } from '@vuelidate/core';
import { required, helpers } from '@vuelidate/validators';
import $ from "jquery";
import { DEFAULT_CONTENT_TYPE, getApiUrl, disableControls }  from '@willsofts/will-app';
import { startWaiting, stopWaiting, submitFailure, detectErrorResponse }  from '@willsofts/will-app';
import { confirmUpdate, confirmSave, confirmDelete, successbox, serializeParameters } from '@willsofts/will-app';
import DialogForm from './DialogForm.vue';

const APP_URL = "/api/emte001";
const defaultData = {
  taskid: "",
  shareflag: "1",
  taskname: "",
  connectid: "",
  taskconfigs: "",
  models: [],
};

export default {
  components: {
    DialogForm
  },
  props: {
    modes: Object,
    labels: Object,
    dataCategory: Object
  },
  emits: ["data-saved","data-updated","data-deleted"],
  setup(props) {
    const mode = ref({action: "new", ...props.modes});
    const localData = ref({ ...defaultData }); 
    const disabledKeyField = ref(false);
    const reqalert = ref(props.labels.empty_alert);
    const requiredMessage = () => {
      return helpers.withMessage(reqalert, required);
    }
    const validateRules = computed(() => { 
      return {
        taskid: { required: requiredMessage() },
        taskname: { required: requiredMessage() },
        connectid: { required: requiredMessage() },
        taskconfigs: { required: requiredMessage() },
      } 
    });
    const v$ = useVuelidate(validateRules, localData, { $lazy: true, $autoDirty: true });
    return { mode, v$, localData, disabledKeyField, reqalert };
  },
  created() {
    watch(this.$props, (newProps) => {      
      this.reqalert = newProps.labels.empty_alert;
    });
  },
  computed: {
    insertMode() {
      return this.mode.action=="insert" || this.mode.action=="new";
    },
    updateMode() {
      return this.mode.action=="update" || this.mode.action=="edit";
    },
  },
  mounted() {
    this.$nextTick(function () {
      $("#modaldialog_layer").find(".modal-dialog").draggable();
    });
  },
  methods: {
    reset(newData,newMode) {
      if(newData) this.localData = {...newData};
      if(newMode) this.mode = {...newMode};
    },
    submit() {      
      this.$emit('update:formData', this.localData);
    },
    async saveClick() {
      console.log("click: save");
      disableControls($("#savebutton"));
      let valid = await this.validateForm();
      if(!valid) return;
      this.startSaveRecord();
    },
    async updateClick() {
      console.log("click: update");
      disableControls($("#updatebutton"));
      let valid = await this.validateForm();
      if(!valid) return;
      this.startUpdateRecord();
    },
    async validateForm(focusing=true) {
      const valid = await this.v$.$validate();
      console.log("validate form: valid",valid);
      console.log("error:",this.v$.$errors);
      if(!valid) {
        if(focusing) {
          this.focusFirstError();
        }
        return false;
      }
      return true;
    },
    focusFirstError() {
      if(this.v$.$errors && this.v$.$errors.length > 0) {
        let input = this.v$.$errors[0].$property;
        let el = this.$refs[input];
        if(el) el.focus(); //if using ref
        else $("#"+input).trigger("focus"); //if using id
      }
    },
    showDialog(callback) {
      //$("#modaldialog_layer").modal("show");
      if(callback) $(this.$refs.dialogForm.$el).on("shown.bs.modal",callback);
      $(this.$refs.dialogForm.$el).modal("show");
    },  
    hideDialog() {
      //$("#modaldialog_layer").modal("hide");
      $(this.$refs.dialogForm.$el).modal("hide");
    },
    resetRecord() {
      //reset to default data 
      this.reset(defaultData,{action:"insert"});
      //reset validator
      this.v$.$reset();
      //enable key field
      this.disabledKeyField = false;
    },
    startInsertRecord() {
      this.resetRecord();
      this.showDialog(() => { this.$refs.taskname.focus(); });
    },
    startSaveRecord() {
      confirmSave(() => {
        this.saveRecord(this.localData);
      });
    },
    startUpdateRecord() {
      confirmUpdate(() => { 
        this.updateRecord(this.localData);
      });
    },
    startDeleteRecord(dataKeys) {
      confirmDelete(Object.values(dataKeys),() => {
        this.deleteRecord(dataKeys);
      });
    },
    saveRecord(dataRecord) {
        let jsondata = {ajax: true};
        let formdata = serializeParameters(jsondata,dataRecord);
        startWaiting();
        $.ajax({
          url: getApiUrl()+APP_URL+"/insert",
          data: formdata.jsondata,
          headers : formdata.headers,
          type: "POST",
          dataType: "json",
          contentType: DEFAULT_CONTENT_TYPE,
          error : function(transport,status,errorThrown) {
            console.error("error: status",status,"errorThrown",errorThrown);
            submitFailure(transport,status,errorThrown);
          },
          success: (data) => {
            stopWaiting();
            console.log("success",data);
            if(detectErrorResponse(data)) return;
            successbox(() => {
              //reset data for new record insert
              this.resetRecord();
              setTimeout(() => { this.$refs.taskname.focus(); },100);
              this.$emit('data-saved',dataRecord,data);
            });
          }
      });
    },
    updateRecord(dataRecord) {
        let jsondata = {ajax: true};
        let formdata = serializeParameters(jsondata,dataRecord);
        startWaiting();
        $.ajax({
          url: getApiUrl()+APP_URL+"/update",
          data: formdata.jsondata,
          headers : formdata.headers,
          type: "POST",
          dataType: "json",
          contentType: DEFAULT_CONTENT_TYPE,
          error : function(transport,status,errorThrown) {
            console.error("error: status",status,"errorThrown",errorThrown);
            submitFailure(transport,status,errorThrown);
          },
          success: (data) => {
            stopWaiting();
            console.log("success",data);
            if(detectErrorResponse(data)) return;
            successbox(() => {
              this.hideDialog();
              this.$emit('data-updated',dataRecord,data);
            });
          }
      });
    },
    retrieveRecord(dataKeys) {
      let jsondata = {ajax: true};
      let formdata = serializeParameters(jsondata,dataKeys);
      startWaiting();
      $.ajax({
        url: getApiUrl()+APP_URL+"/retrieve",
        data: formdata.jsondata,
        headers : formdata.headers,
        type: "POST",
        dataType: "json",
        contentType: DEFAULT_CONTENT_TYPE,
        error : function(transport,status,errorThrown) {
          console.error("retrieveRecord: error: status",status,"errorThrown",errorThrown);
          submitFailure(transport,status,errorThrown);
        },
        success: (data) => {
          stopWaiting();
          console.log("retrieveRecord: success",data);
          if(data.body.dataset) {
            this.reset(data.body.dataset,{action:"edit"});
            this.v$.$reset();
            this.disabledKeyField = true;
            this.showDialog(() => {               
              this.$refs.taskname.focus(); 
              //setTimeout(() => { console.log($("a.model-link:first").html()); $("a.model-link:first").trigger("click"); },1000);
            });
          }
        }
      });	
    },
    deleteRecord(dataKeys) {
      let jsondata = {ajax: true};
      let formdata = serializeParameters(jsondata,dataKeys);
      startWaiting();
      $.ajax({
        url: getApiUrl()+APP_URL+"/remove",
        data: formdata.jsondata,
        headers : formdata.headers,
        type: "POST",
        dataType: "json",
        contentType: DEFAULT_CONTENT_TYPE,
        error : function(transport,status,errorThrown) {
          console.error("deleteRecord: error: status",status,"errorThrown",errorThrown);
          submitFailure(transport,status,errorThrown);
        },
        success: (data) => {
          stopWaiting();
          console.log("deleteRecord: success",data);
          if(detectErrorResponse(data)) return;
          successbox(() => {
            this.$emit('data-deleted',dataKeys,data);
          });
        }
      });	
    },
  }
};
</script>
