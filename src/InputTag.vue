<template>
  <div>
    <div class="width-label tag-input">
      <div v-for="(tag, index) in inputValue" :key="tag" class="tag-input__tag">
        {{ tag }} &nbsp; <span @click="removeTag(index)" class="fa fa-times"></span>
      </div>
      <input type="text" :placeholder="`Enter a ${selectLabel||'Tag'}`" @keydown.enter="addTag" 
      @keydown.188="addTag" @keydown.delete="removeLastTag" class="tag-input__text"/>
    </div>
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
    console.log(this.selectLabel);
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
<style scoped>
.tag-input {
  width: 100%;
  font-size: 0.9em;
  min-height: 50px;
  box-sizing: border-box;
}
.tag-input__tag {
  height: 30px;
  float: left;
  margin-right: 10px;
  background-color: #eee;
  margin-bottom: 10px;
  line-height: 30px;
  padding: 0 5px;
  border-radius: 0px;
}
.tag-input__tag > span {
  cursor: pointer;
  opacity: 0.75;
}
.tag-input__text {
  border: none;
  outline: none;
  /* font-size: 0.9em; */
  /* line-height: 50px; */
  background: none;
}
</style>