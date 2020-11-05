<template>
    <el-table
            :data="data"
            height="500"
            empty-text="Select products to compare"
            style="width: 100%"
    >
        <el-table-column
                prop="thumbnail"
        >
            <template slot-scope="scope">
                <div class="thumbnail-cell">
                    <img class="search-result-img" :src="scope.row.thumbnail" :alt="scope.row.name">
                    <img class="supermarket-badge-img" :src="`./assets/img/${scope.row.supermarket}-icon.png`"
                         :alt="scope.row.supermarket">
                </div>
            </template>
        </el-table-column>
        <el-table-column
                prop="name"
                label="Product"
        >
            <template slot-scope="scope">
                {{ scope.row.name }}
                <a target="_blank" class="el-link" :href="scope.row.link">
                    <el-button size="mini" icon="el-icon-top-right" class="bg-coral button-square"></el-button>
                </a>
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
    name: "CompareTable",
    props: [
        'data',
        'loading'
    ],
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

img.supermarket-badge-img {
    height: 25px;
    position: absolute;
    z-index: 1;
    margin-top: 60px;
    margin-left: -30px;
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