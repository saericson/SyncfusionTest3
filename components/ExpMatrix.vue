<template>
  <v-container>
  
    <div>
      <v-card class="d-flex align-content-start flex-wrap" flat tile>
        <v-btn
          v-for="item in menu"
          :key="item.id"
          class="ml-1"
          text
          small
          @click="runDialog(item.click)"
        >
          {{ item.title }}</v-btn
        >
      </v-card>
    </div>

    <v-row>
      <v-col cols="12">
        <ejs-grid
          id="grid"
          ref="grid"
          :data-source="parms"
          :allow-paging="true"
          :allow-excel-export="true"
          :page-settings="pageSettings"
          :allow-grouping="true"
          :allow-sorting="true"
          :group-settings="groupOptions"
          :toolbar="toolbar"
          :toolbar-click="clickHandler"
          :edit-settings="editSettings"
          :action-begin="actionBegin"
          :action-complete="actionComplete"
        >
          <e-columns>
            <e-column
              v-for="(col, i) in cols"
              :key="i"
              :field="col.col"
              :header-text="col.head"
              :is-primary-key="col.key"
              :width="col.width"
              :text-align="col.align"
              :format="col.format"
              :visible="col.visible"
            ></e-column>
          </e-columns>

        </ejs-grid>
      </v-col>
    </v-row>

  </v-container>
</template>

<script>
import Vue from 'vue'
import {
  GridPlugin,
  Page,
  Toolbar,
  Group,
  Edit,
  Sort,
  ExcelExport,
} from '@syncfusion/ej2-vue-grids'
Vue.use(GridPlugin)
export default {
  name: 'ExpMatrix',
  provide: {
    grid: [Page, Toolbar, Group, Edit, Sort, ExcelExport],
  },
  props: {
  },

  data() {
    return {
      dialog: false,
      menu: [
        { id: 1, title: 'New', click: 'new' },
        { id: 2, title: 'Open', click: 'open' },
        { id: 3, title: 'Save', click: 'save' },
        { id: 4, title: 'Add BU', click: 'addBU' },
        { id: 5, title: 'Delete BU', click: 'delBU' },
        { id: 6, title: 'Rename BU', click: 'renBU' },
        { id: 7, title: 'Add LOB', click: 'addLOB' },
        { id: 8, title: 'Delete LOB', click: 'delLOB' },
        { id: 9, title: 'Rename LOB', click: 'renLOB' },
      ],
      toolbar: [
        'Add',
        'Edit',
        'Delete',
        'Update',
        'Cancel',
        'Print',
        'ExcelExport',
        {
          text: 'Edit Mode',
          tooltipText: 'Toggle Edit Mode',
          prefixIcon: 'e-repeat',
          id: 'grid_toggle',
        },
        {
          text: 'Expand',
          tooltipText: 'Expand All',
          prefixIcon: 'e-expand',
          id: 'grid_expand',
        },
        {
          text: 'Collapse',
          tooltipText: 'Collapse All',
          prefixIcon: 'e-collapse-2',
          id: 'grid_collapse',
        },
        'Search',
      ],
      groupOptions: { columns: ['buName'] },
      editSettings: {
        allowEditing: true,
        allowAdding: true,
        allowDeleting: true,
        mode: 'Normal',
      },
      pageSettings: { pageSizes: [15, 30, 60, 120], pageCount: 4 },
      cols: [
        {
          col: 'key',
          head: 'key',
          width: 20,
          format: 'N0',
          align: 'Left',
          key: true,
          visible: false,
        },
        {
          col: 'buName',
          head: 'Business Unit',
          width: 70,
          format: 'C0',
          align: 'Left',
          key: false,
          visible: true,
        },
        {
          col: 'lobName',
          head: 'Line of Business',
          width: 70,
          format: 'C0',
          align: 'Left',
          key: false,
          visible: true,
        },
        {
          col: 'prem',
          head: 'Premium',
          width: 50,
          format: 'C0',
          align: 'Right',
          key: false,
          visible: true,
        },
        {
          col: 'er',
          head: 'Expense Ratio',
          width: 50,
          format: 'P1',
          align: 'Right',
          key: false,
          visible: true,
        },
        {
          col: 'lr',
          head: 'Loss Ratio',
          width: 50,
          format: 'P1',
          align: 'Right',
          key: false,
          visible: true,
        },
        {
          col: 'dist',
          head: 'Distribution',
          width: 60,
          format: 'CO',
          align: 'Left',
          key: false,
          visible: true,
        },
        {
          col: 'cv',
          head: 'Coef. of Variation',
          width: 40,
          format: 'P1',
          align: 'Right',
          key: false,
          visible: true,
        },
        {
          col: 'cf',
          head: 'Common Factor',
          width: 40,
          format: 'N0',
          align: 'Right',
          key: false,
          visible: true,
        },
        {
          col: 'cfwt',
          head: 'Com. Fac. Weight',
          width: 50,
          format: 'P1',
          align: 'Right',
          key: false,
          visible: true,
        },
      ],
      parms: [],
    }
  },
  computed: {},
    created() {
    this.parms = this.initParms()
  },
  methods: {
    initParms() {
      this.$store.dispatch('simParms/INIT_PARMS')
      const temp = this.arrayCopy(this.$store.state.simParms.parms)
      return temp
    },
    arrayCopy(arr) {
      let temp = arr.map((e, idx) => Object.assign({ idx }, e))
      temp = temp.map((e) => {
        delete e.idx
        return e
      })
      return temp
    },
    clickHandler(args) {
      // alert(args.item.id)
      if (args.item.id === 'grid_collapse') {
        this.$refs.grid.ej2Instances.groupModule.collapseAll()
      }
      if (args.item.id === 'grid_expand') {
        this.$refs.grid.ej2Instances.groupModule.expandAll()
      }
      if (args.item.id === 'grid_excelexport') {
        this.$refs.grid.excelExport()
      }
      if (args.item.id === 'grid_toggle') {
        const gridIns = this.$refs.grid.ej2Instances
        if (gridIns.editSettings.mode === 'Dialog') {
          gridIns.editSettings.mode = 'Normal'
        } else {
          gridIns.editSettings.mode = 'Dialog'
        }
        gridIns.refresh()
      }
    },
    actionBegin(args) {
      // alert(args.requestType)
    },
    actionComplete(args) {
      // update store after grid edit
      if (args.requestType === 'save') {
        alert('saving parms')
        const parms = this.arrayCopy(this.parms)
        this.$store.commit('simParms/SET_PARMS', parms)
      }
      if (args.requestType === 'delete') {
        const parms = this.arrayCopy(this.parms)
        this.$store.commit('simParms/SET_PARMS', parms)
      }
    },
    formatDate(date) {
      const options = { year: 'numeric', month: 'long', day: 'numeric' }
      return new Date(date).toLocaleDateString('en', options)
    },
  },
}
</script>

