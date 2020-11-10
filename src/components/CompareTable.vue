<template>
    <el-table
            :data="data"
            height="500"
            empty-text="Select products to compare"
            style="width: 100%"
    >

        <!-- Product thumbnail image -->
        <el-table-column
                prop="thumbnail"
        >
            <template slot-scope="scope">
                <div class="thumbnail-cell">
                    <!-- Thumbnail -->
                    <img class="search-result-img" :src="scope.row.thumbnail" :alt="scope.row.name">

                    <!-- Supermarket logo badge -->
                    <img class="supermarket-badge-img" :src="`./assets/img/${scope.row.supermarket}-icon.png`"
                         :alt="scope.row.supermarket">
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

                    <!-- Ensure price is displayed as 2 decimal -->
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

/* Broken words break on space, not letter */
.el-table .cell {
    word-break: break-word !important;
}

/* Thumbnail size */
img.search-result-img {
    /*max-height: 100px;*/
    max-width: 100%;
}


/* Supermarket icon badge size */
img.supermarket-badge-img {
    height: 25px;
    position: absolute;
    z-index: 1;
    margin-top: 60px;
    margin-left: -30px;
}

/* Center thumbnail */
.thumbnail-cell {
    width: fit-content;
    margin-left: auto;
    margin-right: auto;
}

/* Prices prepended with '$'
.table-price::before {
    content: "$"
}
</style>