<template>
  <div class="input-block" :class="disable === 'true' ? 'blur-bg' : ''">
    <ValidationProvider :name="name" :rules="rules" v-slot="{ errors }">
      <label v-if="showLabel" :for="name"> {{ label }}</label>
      <div class="input-field" :class="errors[0] || error ? 'error-border' : ''">
        <div v-if="!disable">
          <input
            v-if="type === 'label'"
            :type="text"
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
            @input="$emit('input', $event.target.value)"
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
              @input="$emit('input', inputValue)"
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
              @input="$emit('input', inputValue)"
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
              <!-- <div class="tele-span">{{ defaultValue }}</div> -->
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
            <div style="position:relative;">
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
            @input="$emit('input', $event.target.value)"
            :placeholder="label"
            @focus="setFocus"
            @blur="setBlur"
          />
        </div>
        <div v-else style="color:#ddd">
          {{ value || "Select" }}
        </div>
      </div>
    </ValidationProvider>
  </div>
</template>

<script>
import HSelect from "./Select";
import InputTag from "./InputTag";
import { localize } from "vee-validate/dist/vee-validate.full";
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
  components: { HSelect, Datepicker, InputTag },
  data() {
    return {
      data: "",
      passwordType: "password",
      defaultValue: "",
      defaultNumber: "",
      isFoucs: false,
      errors: [],
      inputValue: null,
      customMessages: {
        en: {
          custom: {
            Label: {
              required: "Please enter label name",
              min: "Atleast minimum 3 characters",
            },
            flatno: {
              required: "Please enter door no",
              min: "Atleast minimum 3 characters",
            },
            street: {
              required: "Please enter street name",
              min: "Atleast minimum 3 characters",
            },
            city: {
              required: "Please enter city name",
              min: "Atleast minimum 3 characters",
            },
            pincode: {
              required: "Please enter pincode",
              digits: "Atleast minimum 6 characters",
            },
            landmark: {
              required: "Please enter landmark",
              min: "Atleast minimum 3 characters",
            },
          },
        },
      },
    };
  },
  created() {
    this.inputValue = this.value;
    localize("en", this.customMessages);
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
    },
    defaultNumber: function(newVal, oldVal) {
      this.inputValue = `${this.defaultValue} ${newVal}`;
      this.$emit('input', this.inputValue);
    },
  },
};
</script>

<style lang="stylus" scoped>
// moved to global style file
.tele-span {
  font-size: 1em;
  width: 50px;
  line-height: 24px;
  padding-left: 10px;
}
.absolute-eye {
  position: absolute;
  right:0;
  top: 5px;
  cursor:pointer;
}
.absolute-eye img {
  width: 16px;
}
</style>
