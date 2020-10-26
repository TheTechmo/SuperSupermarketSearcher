<template>
    <el-table
            v-loading="loading"
            :data="data"
            height="500"
            highlight-current-row
            @current-change="onSelectedRowChange"
            empty-text="Make a search to view results."
            style="width: 100%"
    >
        <el-table-column
                prop="thumbnail"
        >
            <template slot-scope="scope">
                <div class="thumbnail-cell">
                    <img class="search-result-img" :src="scope.row.thumbnail" :alt="scope.row.name">
                </div>
            </template>
        </el-table-column>
        <el-table-column
                prop="name"
                label="Product"
                >
            <template slot-scope="scope">
                <a target="_blank" class="el-link" :href="scope.row.link">{{ scope.row.name }}</a>
            </template>
        </el-table-column>
        <el-table-column
                prop="size"
                label="Size"
        >
        </el-table-column>
        <el-table-column
                prop="price"
                label="Price"
                class="table-price"
        >
            <template slot-scope="scope">
                <span class="table-price">
                    <strong>{{ parseFloat(scope.row.price).toFixed(2) }}</strong>
                </span>
            </template>
        </el-table-column>
    </el-table>
</template>

<script>
export default {
    name: "ResultsTable",
    props: [
        'data',
        'loading'
    ],
    methods: {
        onSelectedRowChange(val) {
            this.$emit('row-selected', val)
        }
    }
}
</script>

<style>
.el-table .cell {
    word-break: break-word !important;
}

img.search-result-img {
    /*max-height: 100px;*/
    max-width: 100%;
}


.thumbnail-cell {
    width: fit-content;
    margin-left: auto;
    margin-right: auto;
}

.table-price::before {
    content: "$"
}

/*.el-table {*/
/*    max-height: 500px;*/
/*    overflow-y: scroll !important;*/
/*}*/

/*.el-table * {*/
/*    overflow-y: scroll !important;*/
/*}*/
</style>