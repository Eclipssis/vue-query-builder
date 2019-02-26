<template>
  <div>
    <div class="data-piker-wrap">
      <label>[[[From]]]</label>
      <div class="input-group">
        <input type="text" v-model="internalFrom">
        <div v-if="dirty && fromErrors">Field FROM is requiried</div>
      </div>
    </div>

    <div class="data-piker-wrap">
      <label>[[[To]]]</label>
      <div class="input-group">
        <input type="text" v-model="internalTo">
        <div v-if="dirty && toErrors">Field TO is requiried</div>
      </div>

    </div>
    <!-- <div v-if="moment(internalTo).isBefore(internalFrom)" class="text-error">[[[Start date cant be after End date]]]</div> -->
  </div>
</template>
<script>


export default {
  props: {
    value: {
      type: Object,
      default () {
        return {
          from: '',
          to: ''
        }
      }
    },
    dirty: {
      type: Boolean,
      default: false
    },
    validator: {
      type: Object,
      default: () => null
    }
  },
  data () {
    return {
      config: {
        allowInput: true,
        wrap: true,
        altFormat: 'Y-m-d',
        dateFormat: 'Y-m-d'
      },
      internalFrom: this.value ? this.value.from : '',
      internalTo: this.value ? this.value.to : ''
    }
  },
  computed: {
    fromErrors () {
      if(this.validator && this.validator.query.value.$model) {
        return !this.validator.query.value.$model.from
      } else {
        return true
      }
    },
    toErrors () {
      if(this.validator && this.validator.query.value.$model) {
        return !this.validator.query.value.$model.to
      } else {
        return true
      }
    }
  },
  watch: {
    value (val) {
      this.internalFrom = val.from
      this.internalTo = val.to
    },
    internalTo (value) {
      // this.$v.internalTo.$touch()
      this.$_emitChange()
    },
    internalFrom (value) {
      // this.$v.internalFrom.$touch()
      this.$_emitChange()
    }
  },
  methods: {
    $_emitChange: function () {
      this.$emit('input', {
        from: this.internalFrom,
        to: this.internalTo
      })
      this.$emit('valueChanged', this.internalFrom, this.internalTo)
    }
  },
  // validations: {
  //   internalFrom: {
  //     contains () {
  //       const regex = /^\d\d\d\d-\d\d-\d\d$/gm
  //       return ((this.internalFrom > '') && regex.test(this.internalFrom))
  //     }
  //   },
  //   internalTo: {
  //     contains () {
  //       const regex = /^\d\d\d\d-\d\d-\d\d$/gm
  //       return ((this.internalTo > '') && regex.test(this.internalTo))
  //     }
  //   }
  // }
}

</script>
<style scoped>

.text-error {
  color: red;
}

.data-piker-wrap {
  display: inline-flex;
  align-items: center;
  margin-right: 8px;
}

.error {
  border: 2px solid red;
  background: #FDD;
}

.error:focus {
  outline-color: #F99;
}

.valid {
  border-color: #5A5;
  background: #EFE;
}

.valid:focus {
  outline-color: #8E8;
}

.data-piker-wrap label {
  display: inline-block;
  vertical-align: middle;
  text-align: right;
  padding: 7px 0;
  color: #808080;
  margin: 0 10px 0 0;
  min-width: 30px;
}
.c-wrapped-extended-search-panel .c-input-text {
  display: inline-block;
}

.c-input-calendar {
  display: inline-block;
  vertical-align: top;
  width: 110px;
}
.c-ico-calendar {
  background-color: #0bafaa;
  padding: 3px;
}
.c-hr {
  color: tomato;
  margin-top: 5px;
}
.c-border-bottom {
    border-bottom: 2px solid #e0e0e0;
    padding: 10px 0;
}
</style>
<style>
.c-input-calendar input {
  background-color: #e0e0e0;
}
.c-input-calendar input:hover {
  background-color: #0ca5ad;
  color: white;
}
</style>

<style>


</style>
