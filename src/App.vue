<template>
  <div id="app">
    <vue-query-builder
      ref="queryBuilder"
      :rules="rules"
      :max-depth="4"
      :labels="labels"
      v-model="query">
      </vue-query-builder>
      <pre>{{ JSON.stringify(query, null, 2) }}</pre>
      <input type="text" v-model="haha">
    <button @click="chef">CLICK</button>
  </div>
</template>

<script>
  
  
  import DateRangePicker from './components/DatePicker.vue';
  import VueQueryBuilder from './VueQueryBuilder.vue';
  const { required, minLength } = require('vuelidate/lib/validators')
  
  function ruleForDate (date) {
    if(date) {
      return date.from.length > 0 && date.to.length > 0
    } else {
      return false
    }
  }

  let rules = [
    {
      type: 'text',
      id: 'first-name',
      label: 'First Name',
      default: '',
      validateMessage:  [
        {required: "Field is requaried"}
      ],
      validator: { required }
    },
    {
      type: 'text',
      id: 'last-name',
      label: 'Last Name',
      validateMessage:  [
        {required: "Field is requaried"}
      ],
      validator: { required }
    },
    {
      type: 'radio',
      id: 'plan-type',
      label: 'Plan Type',
      choices: [
        { label: 'Standard', value: 'standard' },
        { label: 'Premium', value: 'premium' }
      ]
    },
    {
      type: 'custom-component',
      id: 'select',
      label: 'Custom',
      operators: [],
      component: DateRangePicker,
      validateMessage:  [
        { ruleForDate: "Field is requaried" }
      ],
      validator: { ruleForDate }
    }
  ];

  export default {
    name: 'App',
    components: { VueQueryBuilder, DateRangePicker },
    data () {
      return {
        haha: 'so nice',
        rules: rules,
        query: {},
        labels: {
          "matchType": "[[[Match Type]]]",
          "matchTypes": [
            {"id": "all", "label": "[[[All]]]"},
            {"id": "any", "label": "[[[Any]]]"}
          ],
          "ruleTypest": [
            {
              text: '[[[equals]]]',
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
          "addRule": "Add Rule",
          "removeRule": "&times;",
          "addGroup": "Add Group",
          "removeGroup": "&times;",
          "textInputPlaceholder": "value",
        }
      }
    },

    methods: {
      chef () {
        this.$refs.queryBuilder.$v.$touch()
      }
    }, 
    computed: {
      outputFormatted: function() {
        return JSON.stringify(this.output, null, 2);
      }
    }
  }
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
  }
  .aside {
    width: 210px;
    height: 100vh;
    background-color: #c7c7c7;
    padding-top: 40px;
    font-size: 20px;
  }

  /* #wrapper {
    margin-left: 210px;
  } */
</style>
