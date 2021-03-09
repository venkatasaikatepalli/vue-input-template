<template>
  <div class="input-tags-block">
    <div v-for="(tag, index) in inputValue" :key="tag" class="input-tags">
      {{ tag }} &nbsp; <span @click="removeTag(index)" class="fa fa-times"></span>
    </div>
    <input type="text" :placeholder="`Enter a ${selectLabel||'Tag'}`" @keydown.enter="addTag" 
    @keydown.188="addTag" @keydown.delete="removeLastTag" class="input-tags-input"/>
  </div>
</template>
<script>
export default {
  data () {
    return {
      inputValue: []
    }
  },
  created() {
    this.inputValue = this.value || [];
  },
  watch: {
    value: function(newVal, oldVal) {
      this.inputValue = newVal || [];
    }
  },
  methods: {
    addTag (event) {
      event.preventDefault()
      var val = event.target.value.trim()
      if (val.length > 0) {
        this.inputValue.push(val)
        event.target.value = ''
      }
      this.$emit("input", this.inputValue);
    },
    removeTag (index) {
      this.inputValue.splice(index, 1);
      this.$emit("input", this.inputValue);
    },
    removeLastTag(event) {
      if (event.target.value.length === 0) {
        this.removeTag(this.inputValue.length - 1)
      }
    }
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
}
</script>
<style lang="stylus" scoped>
.input-tags-block
  width 100%
  font-size 14px
  min-height 50px
  box-sizing border-box
.input-tags
  float left
  padding 3px 7px
  color #000
  margin 0px 10px 10px 0px
  font-size 14px
  border-radius 3px
  background-color #ffe0e0
  span
    cursor pointer
    margin-left 5px
    opacity 0.75
    color #f00
.input-tags-input
  border none
  outline none
  background none
  font-size 14px
  padding 7px
</style>