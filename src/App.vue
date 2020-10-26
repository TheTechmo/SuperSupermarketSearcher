<template>
    <div id="app">
        <el-container>
            <el-main>
                <el-row :gutter="20">
                    <el-col :span="12" :offset="6">
                        <h1>Super Supermarket Searcher</h1>
                        <p id="description">Select the amount of items you would like returned, enter your search term, wait a few seconds
                        and your search results will appear below! Select a product from each supermarket to compare with
                        each other and they will display, sorted by price, on the right.</p>
                        <el-input placeholder="Search for a product" type="text"
                                  v-model="searchTerm">
                            <el-select v-model="max" slot="prepend" placeholder="Amount" style="width: 75px;">
                                <el-option label="10" value="10"></el-option>
                                <el-option label="25" value="25"></el-option>
                                <el-option label="50" value="50"></el-option>
                                <el-option label="All" value="999999"></el-option>
                            </el-select>
                        </el-input>
                        <el-divider id="divider"></el-divider>
                    </el-col>
                </el-row>
                <el-row :gutter="20">
                    <el-col :span="6">
                        <el-image class="supermarket-banner" :src="getImage('newworld', 'banner')"></el-image>
                        <ResultsTable :data="nwTableData" :loading="loading"
                                      @row-selected="(val) => this.onRowSelect(val, 'newworld')"></ResultsTable>
                    </el-col>
                    <el-col :span="6">
                        <el-image class="supermarket-banner" :src="getImage('paknsave', 'banner')"></el-image>
                        <ResultsTable :data="pnsTableData" :loading="loading"
                                      @row-selected="(val) => this.onRowSelect(val, 'paknsave')"></ResultsTable>
                    </el-col>
                    <el-col :span="6">
                        <el-image class="supermarket-banner" :src="getImage('countdown', 'banner')"></el-image>
                        <ResultsTable :data="cdTableData" :loading="loading"
                                      @row-selected="(val) => this.onRowSelect(val, 'countdown')"></ResultsTable>
                    </el-col>
                    <el-col :span="6">
                        <h3>Compare</h3>
                        <CompareTable :data="compareTableData"></CompareTable>
                    </el-col>
                </el-row>
            </el-main>
        </el-container>
    </div>
</template>

<script>
import ResultsTable from "@/components/ResultsTable";
import CompareTable from "@/components/CompareTable";
const axios = require('axios')
const _ = require('lodash')

export default {
    name: 'App',
    components: {ResultsTable, CompareTable},
    data() {
        return {
            loading: false,
            searchTerm: '',
            nwTableData: null,
            pnsTableData: null,
            cdTableData: null,
            selectedRows: [],
            max: 10
        }
    },
    methods: {
        refreshTables() {
            console.log("refreshing tables")
            const I = this;
            this.selectedRows = []
            if (this.searchTerm === '') {
                I.nwTableData = null;
                I.pnsTableData = null;
                I.cdTableData = null;
                return;
            }

            this.loading = true;
            axios.get('http://localhost:7654/all', {
                params: {
                    search: this.searchTerm,
                    max: this.max
                }
            }).then(function(response) {
                I.nwTableData = response.data['newworld']
                I.pnsTableData = response.data['paknsave']
                I.cdTableData = response.data['countdown']
            }).catch(function(err) {
                console.error(`ERROR: ${err}`)
            }).finally(() => this.loading = false)
        },
        onRowSelect(val, id) {
            this.$set(this.selectedRows, id, val)
        },
        getImage(name, type) {
            return `./assets/img/${name}-${type}.png`
        }
    },
    computed: {
       compareTableData() {
           let ret = []
           for (let key in this.selectedRows) {
               let rows = Object.assign({}, this.selectedRows)

               rows[key]['supermarket'] = key
               ret.push(this.selectedRows[key])
           }
           ret.sort((a, b) => {
               return parseFloat(a.price) - parseFloat(b.price)
           })
           return ret
       }
    },
    watch: {
        searchTerm() {
            console.log("Watching searchterm")
            this.fetchAPI()
        }
    },
    created() {
        this.fetchAPI = _.debounce(function() {
                this.refreshTables()
            }, 1000)
    }
}
</script>

<style>
#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    /*margin-top: 60px;*/
}

.supermarket-banner img {
    width: auto;
    height: auto;
    max-height: 60px !important;
}

p#description {
    margin-bottom: 50px;
}

#divider {
    margin-bottom: 50px;
}
</style>
