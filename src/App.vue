<template>
    <div id="app">
        <el-container>
            <el-main>
                <el-row :gutter="20">
                    <el-col :span="12" :offset="6">
                        <!-- Title -->
                        <h1>Super Supermarket Searcher</h1>
                        <p id="description">Select the amount of items you would like returned, enter your search term,
                            hit enter or press the search button to the right, wait a few seconds
                        and your search results will appear below! Select a product from each supermarket to compare with
                        each other and they will display, sorted by price, on the right.</p>
                        <el-form @submit.native.prevent="refreshTables" id="searchBarForm">
                            <!-- Search Bar -->
                            <el-input placeholder="Search for a product" type="text" id="searchBar"
                                  v-model="searchTerm">
                                <!-- Max products returned dropdown -->
                            <el-select class="bg-coral bg-cut-r" v-model="max" slot="prepend" placeholder="Amount" style="width: 75px;">
                                <el-option label="10" value="10"></el-option>
                                <el-option label="25" value="25"></el-option>
                                <el-option label="50" value="50"></el-option>
                                <el-option label="All" value="999999"></el-option>
                            </el-select>
                                <!-- Search Button -->
                            <el-button class="bg-coral bg-cut-l" native-type="submit" slot="append" icon="el-icon-search">
                            </el-button>
                        </el-input>
                        </el-form>
                        <el-divider id="divider"></el-divider>
                    </el-col>
                </el-row>

                <!-- Results -->
                <el-row :gutter="20">
                    <el-col :span="6">
                        <!-- New World Results -->
                        <el-image class="supermarket-banner" :src="getImage('newworld', 'banner')"></el-image>
                        <ResultsTable :data="nwTableData" :loading="loading"
                                      @row-selected="(val) => this.onRowSelect(val, 'newworld')"></ResultsTable>
                    </el-col>

                    <!-- Paknsave Results -->
                    <el-col :span="6">
                        <el-image class="supermarket-banner" :src="getImage('paknsave', 'banner')"></el-image>
                        <ResultsTable :data="pnsTableData" :loading="loading"
                                      @row-selected="(val) => this.onRowSelect(val, 'paknsave')"></ResultsTable>
                    </el-col>

                    <!-- Countdown results -->
                    <el-col :span="6">
                        <el-image class="supermarket-banner" :src="getImage('countdown', 'banner')"></el-image>
                        <ResultsTable :data="cdTableData" :loading="loading"
                                      @row-selected="(val) => this.onRowSelect(val, 'countdown')"></ResultsTable>
                    </el-col>

                    <!-- Comparing -->
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

export default {
    name: 'App',
    components: {ResultsTable, CompareTable},
    data() {
        return {
            loading: false, // Display loading indicators over results tables?
            searchTerm: '', // User's input search term
            nwTableData: null, // New World products
            pnsTableData: null, // Paknsave products
            cdTableData: null, // Countdown products
            selectedRows: null, // Products to compare
            max: 10 // Max returned products
        }
    },
    methods: {
        /**
         * Fetch new products based on the search term and display them
         */
        refreshTables() {
            const I = this;
            this.selectedRows = null

            // If the search bar is empty, remove all products from displaying and return
            if (this.searchTerm.trim() === '') {
                I.nwTableData = null;
                I.pnsTableData = null;
                I.cdTableData = null;
                return;
            }

            // Show loading indicators
            this.loading = true;

            // Fetch products
            axios.get('https://supermarketsearcher.apis.zachb.nz/all', {
                params: {
                    search: this.searchTerm, // Search term from search bar
                    max: this.max // Max products to return
                }
            }).then(function(response) {

                // Set supermarket data from request response
                I.nwTableData = response.data['newworld']
                I.pnsTableData = response.data['paknsave']
                I.cdTableData = response.data['countdown']
            }).catch(function(err) {
                console.error(`ERROR: ${err}`) // Something went wrong
            }).finally(() => this.loading = false) // Stop displaying loading indicators regardless of return
        },

        /**
         * Called when a product's row is selected in a results table.
         * Adds said product to this.selectedRows for storage
         *
         * @param val A product object | null
         * @param id The name of a supermarket
         */
        onRowSelect(val, id) {

            // Don't do anything if val is null
            if (val == null) {
                return
            }

            // Init selectedRows if null
            if (this.selectedRows == null) {
                this.selectedRows = {}
            }

            // Reactively add val to selected rows under id.
            this.$set(this.selectedRows, id, val)
        },

        /**
         * Simple helper function to generate the url of a supermarket image asset
         *
         * @param name The name of the supermarket
         * @param type The type of image to get ("banner" | "icon")
         * @returns {string}
         */
        getImage(name, type) {
            return `./assets/img/${name}-${type}.png`
        }
    },
    computed: {
        /**
         * Format data for the compare table.
         * @returns {[]}
         */
        compareTableData() {
            const selectedRows = this.selectedRows
            let ret = []

            // If no rows are selected, return no rows to compare.
            if (selectedRows == null) {
                return ret
            }

            // Adds a supermarket name to each row in selectedRows
            for (let key in selectedRows) {
                let rows = Object.assign({}, selectedRows)

                if (key in rows) {
                    rows[key]['supermarket'] = key
                    ret.push(selectedRows[key])
                }
            }

            // Sort products by price
            ret.sort((a, b) => {
                return parseFloat(a.price) - parseFloat(b.price)
            })
            return ret
        }
    },
    watch: {
        /**
         * Wipes all data when the search bar becomes blank
         */
        searchTerm() {
            if (this.searchTerm.trim() === '') {
                this.nwTableData = null
                this.pnsTableData = null
                this.cdTableData = null
                this.selectedRows = null
            }
        },
    }
}
</script>

<style>

/* Basic styling */
#app {
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    border-top: 20px solid lightcoral;
    /*margin-top: 60px;*/
}

#app, body {
        font-family: Avenir, Helvetica, Arial, sans-serif !important;
}


/* So that the top bar of colour fits snugly into the page */
body {
    margin: 0 !important;

}

/* No sort of highlight or border around the search bar to give it a clean look */
#searchBar {
    border: none;
}
#searchBarForm .el-input-group__append, #searchBarForm .el-input-group__prepend {
    border: none !important;
}
#searchBar input:active {
    outline: none !important;
}



/* Light red background */
.bg-coral {
    background: lightcoral !important;
}
.bg-coral, .bg-coral *, .bg-cut-r .el-input__icon:before {
    color: white;
}


/* Remove right and left respectively border curves */
.bg-cut-r {
    border-right: none !important;
    border-radius: 4px !important;
    border-bottom-right-radius: 0 !important;
    border-top-right-radius: 0 !important;
}
.bg-cut-l {
    border-left: none !important;
    border-bottom-left-radius: 0 !important;
    border-top-left-radius: 0 !important;
}

/* Size of supermarket banners */
.supermarket-banner img {
    width: auto;
    height: auto;
    max-height: 60px !important;
}


/* Give the description and divider a bit of space at the bottom */
p#description, #divider {
    margin-bottom: 50px;
}
</style>
