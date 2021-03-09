<template>
  <div>
    <div class="width-label" :id="myID" v-if="type === 'single'">
      <div class="h-select" @click="activeDropDown()">
        {{ getSelectedLabel() }}
      </div>
      <div class="h-options" v-if="visible" @click.stop>
        <ul class="h-options-list">
          <template v-for="i in options">
            <li class="options" v-if="typeof i === 'object'" :key="i">
              <input
                type="checkbox"
                :name="i[1]"
                :id="i[1]"
                @change="setSingle($event)"
                :value="i[1]"
                :checked="singleValue === i[1]"
              />
              <label :for="i[1]">{{ i[0] }}</label>
            </li>
            <li class="options" v-else :key="i" :value="i">
              <input
                type="checkbox"
                :name="i"
                :id="i"
                @change="setSingle($event)"
                :value="i"
                :checked="singleValue === i"
              />
              <label :for="i">{{ i }}</label>
            </li>
          </template>
        </ul>
        <div v-if="label==='Client' || label==='Vendor'" class="pad-10" style="border-top:1px solid #ddd;" @click="$emit('addnew', label)">
          + Add New {{label}}
        </div>
      </div>
    </div>
    <div class="width-label" :id="myID" v-if="type === 'multiple'">
      <div
        class="h-select"
        @click="activeDropDown()"
        style="display: flex;align-items: center; justify-content: flex-start; flex-wrap:wrap;"
      >
        <template v-if="multipleValue.length > 0">
          <template v-for="itm in options">
            <template v-if="typeof itm === 'object' && multipleValue.indexOf(itm[1])!==-1">
              <div style="margin: 0px 2px" :key="itm[1]">
                <span class="h-tag">{{ itm[0] }}</span>
              </div>
            </template>
            <template v-else>
              <div style="margin: 0px 2px" :key="itm" v-if="multipleValue.indexOf(itm)!==-1">
                <span class="h-tag">{{ itm }}</span>
              </div>
            </template>
          </template>
        </template>
        <template v-else>
          {{selectLabel || "Select"}}
        </template>
      </div>
      <div class="h-options" v-if="visible" @click.stop>
        <ul class="h-options-list">
          <template v-for="i in options">
            <li class="options" v-if="typeof i === 'object'" :key="i">
              <input
                type="checkbox"
                :name="i[1]"
                :id="i[1]"
                @change="setMultiple($event)"
                :value="i[1]"
                :checked="multipleValue.indexOf(i[1]) !== -1"
              />
              <label :for="i[1]">{{ i[0] }}</label>
            </li>
            <li class="options" v-else :key="i" :value="i">
              <input
                type="checkbox"
                :name="i"
                :id="i"
                @change="setMultiple($event)"
                :value="i"
                :checked="multipleValue.indexOf(i) !== -1"
              />
              <label :for="i">{{ i }}</label>
            </li>
          </template>
        </ul>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      singleValue: null,
      multipleValue: [],
      myID: Math.random()
        .toString(36)
        .substring(7),
      ele: null,
      visible: false,
    };
  },
  created() {
    this.type === "multiple"
      ? (this.multipleValue = this.value)
      : (this.singleValue = this.value);
  },
  watch: {
    value: function(newVal, oldVal) {
      this.type === "multiple"
        ? (this.multipleValue = newVal)
        : (this.singleValue = newVal);
    },
  },
  methods: {
    getSelectedLabel() {
      let label = this.selectLabel || "Select";
      if(this.type === "multiple") {
        label = this.multipleValue || (this.selectLabel || "Select");
      } else {
        if(this.singleValue) {
          this.options.forEach(item => {
            if(typeof item === 'object' && item[1]===this.singleValue) {
              label = item[0];
            } else if(item === this.singleValue){
              label = this.singleValue;
            }
          });
        }
      }
      return label;
    },
    activeDropDown() {
      if (this.visible) {
        return this.hide();
      }
      return this.show();
    },
    show() {
      this.visible = true;
      setTimeout(() => document.addEventListener("click", this.hide), 0);
    },
    hide() {
      this.visible = false;
      document.removeEventListener("click", this.hide);
    },
    setSingle(event) {
      this.singleValue = event.target.value;
      this.$emit("input", this.singleValue);
      this.hide();
    },
    setMultiple(event) {
      if (event.target.checked) {
        this.multipleValue = [...this.multipleValue, event.target.value];
      } else {
        this.multipleValue.splice(
          this.multipleValue.indexOf(event.target.value),
          1
        );
      }
      this.$emit("input", this.multipleValue);
    },
  },
  props: {
    type: {
      required: false,
      default: "single",
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
    rules: {
      required: false,
    },
    options: {
      required: false,
    },
    error: {
      required: false,
    },
  },
};
</script>
<style lang="stylus" scoped>
.width-label
  position relative
  cursor pointer
  .h-options
    min-width: 80px;
    position: absolute;
    top calc(100% + 11px)
    left -11px
    right -11px
    background-color #fff
    border 1px solid #ddd
    z-index 20
    box-shadow 0px 5px 10px rgba(0,0,0,0.09)
    .h-options-list
      list-style none
      margin 0px
      padding 0px
      display flow-root
      max-height 30vh
      overflow-y auto
      li
        padding 8px 10px
        letter-spacing 0.4px
        input
          display inline-block
          width auto
          position relative
          top 2px
          margin-right 3px
        label
          width auto
          font-weight 400
          color #000
          cursor pointer
.width-label label {
  width: 100%;
  margin: 0px;
  color: #666;
  font-weight: bold;
  font-size: 12px;
  font-size: 12px;
}
.width-label input {
  width: 100%;
  border: none;
  box-shadow: none;
  font-size: 14px;
  line-height: 14px;
  outline: none;
  background-color: transparent;
}
.h-select {
  line-height: 24px;
  padding-right: 20px;
  min-width: 50px;
}
.h-select:after {
  content: '>';
  position: absolute;
  top:0;
  right: 0;
  font-size: 16px;
  transform: rotate(90deg);
  color: red;
  transition: 0.2s all ease-in-out;
}
</style>
