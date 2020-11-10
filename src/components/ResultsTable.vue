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

        <!-- Thumbnail image -->
        <el-table-column
                prop="thumbnail"
        >
            <template slot-scope="scope">
                <div class="thumbnail-cell">
                    <img class="search-result-img" :src="scope.row.thumbnail" :alt="scope.row.name">
                </div>
            </template>
        </el-table-column>

        <!-- Product name -->
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

        <!-- Product size -->
        <el-table-column
                prop="size"
                label="Size"
        >
        </el-table-column>

        <!-- Product price -->
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
        /**
         * Emits the 'row-selected' event to the parent to tell it that the user has selected a product to compare
         * @param val A product object | null
         */
        onSelectedRowChange(val) {
            this.$emit('row-selected', val)
        }
    }
}
</script>

<style>

/* Broken words break on spaces not letters */
.el-table .cell {
    word-break: break-word !important;
}

/* Product image sizing */
img.search-result-img {
    /*max-height: 100px;*/
    max-width: 100%;
}

/* Center the thumbnail cell */
.thumbnail-cell {
    width: fit-content;
    margin-left: auto;
    margin-right: auto;
}

/* Prices display with '$' prepended */
.table-price::before {
    content: "$"
}

/* Square the popout button */
.bg-coral.button-square {
    padding-left: 7px !important;
    padding-right: 7px !important;
}

/* Make rows look clickable */
tr.el-table__row {
    cursor: pointer;
}
</style>