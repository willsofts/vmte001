<template>
              <div class="row row-height">
                <div class="col-height col-md-10">
<label class="control-label">{{ labels.modelname_label }}</label>
<div class="input-group has-validation" :class="{'has-error': v$.modelname.$error}">
<input type="text" v-model="localData.modelname" :id="'modelname_' + localData.modelid" class="form-control input-md irequired alert-input model-name" maxlength="150" />
</div>
<span v-if="v$.modelname.$error" class="has-error">{{ v$.modelname.$errors[0].$message }}</span>
                </div>
              </div>
              <div class="row row-height">
                <div class="col-height col-md-5">
<label class="control-label">{{ labels.tablename_label }}</label>
<div class="input-group has-validation" :class="{'has-error': v$.tablename.$error}">
<input type="text" v-model="localData.tablename" :id="'tablename_' + localData.modelid" class="form-control input-md irequired alert-input table-name" maxlength="50" />
</div>
<span v-if="v$.tablename.$error" class="has-error">{{ v$.tablename.$errors[0].$message }}</span>
                </div>
              </div>
              <div class="row row-height">
                <div class="col-height col-md-12">
<label class="control-label">{{ labels.tablefields_label }}</label>
<div class="input-group has-validation" :class="{'has-error': v$.tablefields.$error}">
<textarea v-model="localData.tablefields" :id="'tablefields_' + localData.modelid" class="form-control input-md irequired alert-input table-fields validate-json" rows="15" />
</div>
<span v-if="v$.tablefields.$error" class="has-error">{{ v$.tablefields.$errors[0].$message }}</span>
                </div>
              </div>
              <div class="row row-height">
                <div class="col-height col-md-12">
<label class="control-label">{{ labels.tablesettings_label }}</label>
<div class="input-group has-validation" :class="{'has-error': v$.tablesettings.$error}">
<textarea v-model="localData.tablesettings" :id="'tablesettings_' + localData.modelid" class="form-control input-md irequired alert-input table-settings validate-json" rows="5" />
</div>
<span v-if="v$.tablesettings.$error" class="has-error">{{ v$.tablesettings.$errors[0].$message }}</span>
                </div>
              </div>
              <div class="row row-height">
                <div class="col-height col-md-12">
<label class="control-label">{{ labels.submodels_label }}</label>
<div class="input-group has-validation" :class="{'has-error': v$.submodels.$error}">
<textarea v-model="localData.submodels" :id="'submodels_' + localData.modelid" class="form-control input-md irequired alert-input table-settings validate-json" rows="12" />
</div>
<span v-if="v$.submodels.$error" class="has-error">{{ v$.submodels.$errors[0].$message }}</span>
                </div>
              </div>
</template>
<script>
import { ref, computed, watch } from 'vue';
import { useVuelidate } from '@vuelidate/core';
import { helpers } from '@vuelidate/validators';
import $ from "jquery";
import { validJSON, validJSONArray } from "../assets/js/app.libs.js";

const defaultData = {
  modelid: "",
  modelname: "",
  tablename: "",
  tablefields: "",
  tablesettings: "",
  submodels: "",
};

export default {
  props: {
    modes: Object,
    labels: Object,
    dataCategory: Object,
    dataModel: Object,
    index: {
      type: Number,
      required: true
    },
  },
  setup(props) {
    const mode = ref({action: "new", ...props.modes});
    const localData = ref(props.dataModel); 
    const reqalert = ref(props.labels.empty_alert);
    const requiredMessage = () => {
      return helpers.withMessage(reqalert,helpers.withParams({
        type: "required",
        index: props.index,
        modelid: props.dataModel.modelid,
      },value => !!value)); 
    }
    const validateRules = computed(() => { 
      return {
        modelname: { required: requiredMessage() },
        tablename: { required: requiredMessage() },
        tablefields: { 
          required: requiredMessage(),
          validFields: helpers.withMessage("Invalid JSON format setting", helpers.withParams({ type: "required", index: props.index },value => validJSON(value)) )
        },
        tablesettings: {  
          validSettings: helpers.withMessage("Invalid JSON format setting", helpers.withParams({ type: "required", index: props.index },value => validJSON(value)) )
        },
        submodels: {  
          validSubModels: helpers.withMessage("Invalid JSON array format setting", helpers.withParams({ type: "required", index: props.index },value => validJSONArray(value)) )
        },
      } 
    });
    const v$ = useVuelidate(validateRules, localData, { $lazy: true, $autoDirty: true, $scope: "entry" });
    return { mode, v$, localData, reqalert };
  },
  created() {
    watch(this.$props, (newProps) => {      
      this.reqalert = newProps.labels.empty_alert;
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
    resetRecord() {
      //reset to default data 
      this.reset(defaultData,{action:"insert"});
      //reset validator
      this.v$.$reset();
    },
  }
};
</script>
