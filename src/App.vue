<template>
  <div id="app">
    
    <vue-query-builder
      :rules="rules"
      :max-depth="2"
      :labels="labels"
      v-model="query">
      </vue-query-builder>
      <pre>{{ JSON.stringify(query, null, 2) }}</pre>
    <button @click="chef">CLICK</button>
  </div>
</template>

<script>
  
  import VueQueryBuilder from './VueQueryBuilder.vue';

  let rules = [
  {
    type: 'text',
    id: 'first-name',
    label: 'First Name'
  },
  {
    type: 'text',
    id: 'last-name',
    label: 'Last Name'
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
    choices: [
      { label: 'red', value: 'Red' },
      { label: 'orange', value: 'Orange' },
      { label: 'yellow', value: 'Yellow' },
      { label: 'green', value: 'Green' },
      { label: 'blue', value: 'Blue' },
      { label: 'indigo', value: 'Indigo' },
      { label: 'violet', value: 'Violet' }
    ]
  }
];


  export default {
    name: 'App',
    components: { VueQueryBuilder },
    data () {
      return {
        rules: rules,
        query: {},
        labels: {
          "matchType": "[[[Match Type]]]",
          "matchTypeAll": "All",
          "matchTypeAny": "Any",
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
        let q = {
          "logicalOperator": "All",
          "children": [
            {
              "type": "query-builder-rule",
              "query": {
                "rule": "first-name",
                "selectedOperator": "equals",
                "selectedOperand": "First Name",
                "value": null
              }
            },
            {
              "type": "query-builder-rule",
              "query": {
                "rule": "select",
                "selectedOperator": "=",
                "selectedOperand": "Color",
                "value": "Red"
              }
            }
          ]
        }
        this.query = q
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
