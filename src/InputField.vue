<template>
  <div class="vue-input-template" :class="disable === 'true' ? 'blur-bg' : ''">
    <ValidationProvider :name="name" :rules="rules" v-slot="{ errors }">
      <label v-if="showLabel" :for="name"> {{ label }}</label>
      <div class="input-field" :class="errors[0] || error ? 'error-border' : ''">
        <div v-if="!disable">
          <input
            v-if="type === 'label'"
            type="text"
            :name="name"
            v-model="inputValue"
            :placeholder="label"
            autocomplete="false"
            disabled
          />
          <datepicker 
          v-else-if="type === 'date'"
          v-model="inputValue" name="uniquename"></datepicker>
          <textarea
            v-else-if="type === 'textarea'"
            :name="name"
            :rows="rows"
            v-model="inputValue"
            @input="$emit('input', $event.target.value);$emit('change', $event.target.value)"
            :placeholder="label"
            @focus="setFocus"
            @blur="setBlur"
            :cols="cols"
            autocomplete="false"
            style="border:none;width:100%;"
          >
          </textarea>
          <template v-else-if="type === 'select'">
            <h-select
              :options="options"
              :select-label="selectLabel"
              :label="label"
              :type="multiple ? 'multiple' : 'single'"
              :name="name"
              :value="inputValue"
              v-model="inputValue"
              @input="$emit('input', inputValue);$emit('change', inputValue)"
              @addnew="initiateAddNewEvent"
            ></h-select>
          </template>
          <template v-else-if="type === 'tags'">
            <input-tag
              :options="options"
              :select-label="selectLabel"
              :label="label"
              :type="multiple ? 'multiple' : 'single'"
              :name="name"
              :value="inputValue"
              v-model="inputValue"
              @input="$emit('input', inputValue);$emit('change', inputValue)"
              @addnew="initiateAddNewEvent"
            ></input-tag>
          </template>
          <template v-else-if="type === 'telephone'">
            <div class="d-flex">
              <h-select
                :options="$constants.countryCodes"
                :label="label"
                :type="multiple ? 'multiple' : 'single'"
                :name="name"
                :value="defaultValue"
                v-model="defaultValue"
              ></h-select>
              <input
                style="padding-left: 10px"
                autocomplete="false"
                :class="errors[0] === 'ErrorBlock'"
                type="text"
                :name="name"
                :rows="rows"
                :cols="cols"
                v-model="defaultNumber"
                :placeholder="label"
                @focus="setFocus"
                @blur="setBlurandUpdate"
              />
            </div>
          </template>
          <template v-else-if="type === 'password'">
            <div class="input-field-layer">
              <input
                :class="errors[0] === 'ErrorBlock'"
                autocomplete="false"
                :type="passwordType"
                :name="name"
                :rows="rows"
                :cols="cols"
                v-model="inputValue"
                @input="$emit('input', $event.target.value)"
                :placeholder="label"
                @focus="setFocus"
                @blur="setBlur"
              />
              <span @click="changeType()" class="absolute-eye"
                ><img
                  src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABgAAAAYCAYAAADgdz34AAAABmJLR0QA/wD/AP+gvaeTAAABQElEQVRIie3UTSsHURTH8Y+yIQqx8ZBYWEhEWLGWPa9CFrwFpbwCz/IvG16DHUvsyE7ytCF5KEUeFnPVNGb8Bwub/69OTfec8/3NPXPnUtJ/qyxHTTkG0IEGvOMax9jD62/Ne7GO+wBNizusofsn4DoUAuAByxhDGypDtGMcK3jEG1ZRWwzehTM8YxY1OV9oDi84RWdWYS9ucYH+RK4Mw5gOMeTr9xvEJW7Qk4Q3BPdTtCZyTdj1df47IRdXm2gCJ6iPJzbwhL5EQyWOUuCfcYiKRM9AYBXiC2+YSW5LNI4s+GdMpfTNio5vHyyJjmJVSuF2DoPtlL5q0QlcgKv4dhLaz2FwkNFbCGzjaM4o2sphsJnR2xLY32o0h8FIMUgxLX4Dn/8rnOiHmsR5DHyGCTkuyzy3aby2MTxfBqOSSuIDl0l8CXQfe4sAAAAASUVORK5CYII="
              /></span>
            </div>
          </template>
          <input
            v-else
            :class="errors[0] === 'ErrorBlock'"
            autocomplete="false"
            :type="type"
            :name="name"
            :rows="rows"
            :cols="cols"
            v-model="inputValue"
            @input="$emit('input', $event.target.value);$emit('change', $event.target.value)"
            :placeholder="label"
            @focus="setFocus"
            @blur="setBlur"
          />
        </div>
        <div v-else class="disabled-text">
          {{ value || "Select" }}
        </div>
      </div>
    </ValidationProvider>
  </div>
</template>

<script>
import { extend } from "vee-validate";
import * as rules from "vee-validate/dist/rules";
import { messages } from "vee-validate/dist/locale/en.json";
import { ValidationProvider } from "vee-validate";
Object.keys(rules).forEach((rule) => {
  extend(rule, {
    ...rules[rule], // copies rule configuration
    message: messages[rule], // assign message
  });
});
import HSelect from "./Select";
import InputTag from "./InputTag";
import Datepicker from 'vuejs-datepicker'
export default {
  props: {
    type: {
      required: true,
    },
    name: {
      required: false,
    },
    label: {
      required: false,
    },
    selectLabel: {
      required: false,
    },
    value: {
      required: true,
    },
    disable: {
      required: false,
      default: false,
    },
    message: {
      required: false,
    },
    rules: {
      required: false,
    },
    rows: {
      required: false,
    },
    cols: {
      required: false,
    },
    options: {
      required: false,
    },
    error: {
      required: false,
    },
    showLabel: {
      required: false,
      default: false,
    },
    multiple: {
      required: false,
      default: false,
    },
  },
  components: { HSelect, Datepicker, InputTag, ValidationProvider },
  data() {
    return {
      data: "",
      passwordType: "password",
      defaultValue: "",
      defaultNumber: "",
      isFoucs: false,
      errors: [],
      inputValue: null,
    };
  },
  created() {
    this.inputValue = this.value;
  },
  methods: {
    initiateAddNewEvent(value) {
      this.$emit('new', value);
    },
    setInputValue(value) {
      console.log(value);
      var phoneno = /^[0-9]+$/;
      if (value.match(phoneno)) {
        this.inputValue = value;
        console.log("matched");
      } else {
        this.inputValue = this.inputValue.slice(0, -1);
      }
    },
    changeType() {
      if (this.passwordType === "password") {
        this.passwordType = "text";
      } else {
        this.passwordType = "password";
      }
    },
    handleEvent() {
      debugger;
    },
    setFocus() {
      this.isFoucs = true;
    },
    setBlur() {
      this.isFoucs = false;
    },
    setBlurandUpdate() {
      this.isFoucs = false;
    },
  },
  watch: {
    value: function(newVal, oldVal) {
      if(this.type === 'telephone') {
        if(newVal && newVal.toString().indexOf(' ')!==-1) {
          this.defaultValue = newVal.split(' ')[0];
          this.defaultNumber = newVal.split(' ')[1];
        } else {
          this.defaultNumber = newVal;
        }
      } else {
        this.inputValue = newVal;
      }
    },
    defaultValue: function(newVal, oldVal) {
      this.inputValue = `${newVal} ${this.defaultNumber}`;
      this.$emit('input', this.inputValue);
      this.$emit('change', this.inputValue);
    },
    defaultNumber: function(newVal, oldVal) {
      this.inputValue = `${this.defaultValue} ${newVal}`;
      this.$emit('input', this.inputValue);
      this.$emit('change', this.inputValue);
    },
  },
};
</script>

<style lang="stylus" scoped>
.vue-input-template
  margin-bottom 2em
  label
    width 100%
    margin 0px
  .input-field
    background-color #fff
    border 1.5px solid #ddd
    border-radius 3px
    padding 14px
    transition 0.2s all ease-in-out
    .disabled-text
      color #ddd
    input, textarea
      width 100%
      border none
      box-shadow none
      font-size 14px
      line-height 14px
      outline none
      background-color transparent
      &::placeholder
        color #666
    &.error-border
      border 1.5px solid red
    .input-field-layer
      position relative
      .absolute-eye
        position absolute
        right 0
        top 5px
        cursor pointer
        img
          width 16px
  &.blur-bg
    background-color #eee
</style>
