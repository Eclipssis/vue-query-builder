<template>
  <div class="vue-query-builder" :class="{ 'vue-query-builder-styled': styled }">
    
    <query-builder-group
      :index="0"
      :query.sync="query"
      :ruleTypes="ruleTypes"
      :rules="mergedRules"
      :maxDepth="maxDepth"
      :depth="depth"
      :styled="styled"
      :labels="mergedLabels"
      :validator="$v"
      :buttonLocation='buttonLocation'
      type="query-builder-group"
      >
      <slot></slot>
      </query-builder-group>
  </div>
</template>

<script>
import QueryBuilderGroup from './components/QueryBuilderGroup.vue';
import deepClone from './utilities.js';
const { required } = require('vuelidate/lib/validators')

var defaultLabels = {
  matchType: "Match Type",
  matchTypes: [
    {"id": "all", "label": "All"},
    {"id": "any", "label": "Any"},
  ],
  ruleTypest: [
    {
      text: 'equals',
      value: 'equals'
    },
    {
      text: 'does not equal',
      value: 'does not equal'
    },
    {
      text: 'contains',
      value: 'contains'
    },
    {
      text: 'does not contain',
      value: 'does not contain'
    },
    {
      text: 'is empty',
      value: 'is empty'
    },
    {
      text: 'is not empty',
      value: 'is not empty'
    },
    {
      text: 'begins with',
      value: 'begins with'
    },
    {
      text: 'ends with',
      value: 'ends with'
    }
  ],
  addRule: "Add Rule",
  removeRule: "&times;",
  addGroup: "Add Group",
  removeGroup: "&times;",
  textInputPlaceholder: "value",
};

export default {
  name: 'vue-query-builder',

  components: {
    QueryBuilderGroup
  },

  props: {
    rules: Array,
    labels: {
      type: Object,
      default () {
        return defaultLabels;
      }
    },
    styled: {
      type: Boolean,
      default: true
    },
    maxDepth: {
      type: Number,
      default: 3,
      validator: function (value) {
        return value >= 1
      }
    },
    buttonLocation: String,
    value: Object
  },

  data () {
    return {
      depth: 1,
      query: {
        logicalOperator: this.labels.matchTypes[0].id,
        children: []
      },
      ruleTypes: {
        "text": {
          operators: this.labels.ruleTypest,
          inputType: "text",
          id: "text-field"
        },
        "numeric": {
          operators: ['=','<>','<','<=','>','>='],
          inputType: "number",
          id: "number-field"
        },
        "custom": {
          operators: [],
          inputType: "text",
          id: "custom-field"
        },
        "radio": {
          operators: [],
          choices: [],
          inputType: "radio",
          id: "radio-field"
        },
        "checkbox": {
          operators: [],
          choices: [],
          inputType: "checkbox",
          id: "checkbox-field"
        },
        "select": {
          operators: [],
          choices: [],
          inputType: "select",
          id: "select-field"
        },
        "multi-select": {
          operators: ['='],
          choices: [],
          inputType: "select",
          id: "multi-select-field"
        },
      }
    }
  },

  computed: {
    mergedLabels () {
      return Object.assign({}, defaultLabels, this.labels);
    },

    mergedRules () {
      var mergedRules = [];
      var vm = this;

      vm.rules.forEach(function(rule){
        if ( typeof vm.ruleTypes[rule.type] !== "undefined" ) {
          mergedRules.push( Object.assign({}, vm.ruleTypes[rule.type], rule) );
        } else {
          mergedRules.push( rule );
        }
      });

      return mergedRules;
    }
  },
  validations() {
    return this.queryBuilderValidation()
  },
  mounted () {
    this.$watch(
      'query',
      newQuery => {
        if (JSON.stringify(newQuery) !== JSON.stringify(this.value)) {
        this.$emit('input', deepClone(newQuery));
        }
      }, {
      deep: true
    });

    this.$watch(
      'value',
      newValue => {
        if (JSON.stringify(newValue) !== JSON.stringify(this.query)) {
          this.query = deepClone(newValue);
        }
      }, {
      deep: true
    });

    if ( typeof this.$options.propsData.value !== "undefined" ) {
      this.query = Object.assign(this.query, this.$options.propsData.value);
    }
  },
  watch: {
    value (val) {
      this.query = val
  }
  },
  methods: {
    
    createValidateLevel (filterItem) {
      const validateCondition = filterItem.type === "query-builder-rule" && filterItem.query.selectedOperator !== "is empty" && filterItem.query.selectedOperator !== "is not empty"
      let validationsRules = validateCondition ? this.rules.find(item => {
        return item.id === filterItem.query.rule
      }) : undefined

      let validator = {
        query: {
          children: filterItem.query.children ? {...filterItem.query.children.map(this.createValidateLevel)} : {}
        }
      }

      if(validationsRules && validationsRules.validator) {
        validator.query.value = validationsRules.validator
      }
      
       return validator
    },
    queryBuilderValidation () {
      
      let validator = {
        query: {
          children: this.value.children ? {...this.value.children.map(this.createValidateLevel)} : {}
        }
      }
      
      return validator
    }
  },
}
</script>

<style>
  .vue-query-builder-styled .vqb-group .rule-actions {
    margin-bottom: 20px;
  }

  .vue-query-builder-styled .vqb-rule {
    margin-top: 15px;
    margin-bottom: 15px;
    background-color: #f5f5f5;
    border-color: #ddd;
    padding: 15px;
  }

  .vue-query-builder-styled .vqb-rule label {
    margin-right: 10px;
  }

  .vue-query-builder-styled .vqb-group.depth-1 .vqb-rule,
  .vue-query-builder-styled .vqb-group.depth-2 {
    border-left: 2px solid #8bc34a;
  }

  .vue-query-builder-styled .vqb-group.depth-2 .vqb-rule,
  .vue-query-builder-styled .vqb-group.depth-3 {
    border-left: 2px solid #00bcd4;
  }

  .vue-query-builder-styled .vqb-group.depth-3 .vqb-rule,
  .vue-query-builder-styled .vqb-group.depth-4 {
    border-left: 2px solid #ff5722;
  }

  .vue-query-builder-styled .close {
    opacity: 1;
    color: rgb(150,150,150);
  }

  @media (min-width: 768px) {
    .vue-query-builder-styled .vqb-rule.form-inline .form-group {
      display: block;
    }
  }
</style>