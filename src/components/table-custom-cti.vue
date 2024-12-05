<template>
    <div>
        <div class="table-toolbar" v-if="hasToolbar">
            <div class="table-toolbar-left">
                <slot name="toolbarBtn"></slot>
            </div>
        </div>
        <el-table class="mgb20" :style="{ width: '100%' }" border :data="tableData" :row-key="rowKey"
            @selection-change="handleSelectionChange" table-layout="auto">
            <el-table-column type="selection" width="55"/>
            <template v-for="item in columns" :key="item.prop">
                <el-table-column v-if="item.visible" :prop="item.prop" :label="item.label" :width="item.width" show-overflow-tooltip
                    :type="item.type" :align="item.align || 'center'">

                    <template #default="{ row, column, $index }" v-if="item.type === 'index'">
                        {{ getIndex($index) }}
                    </template>
                    <template #default="{ row, column, $index }" v-if="!item.type">
                        <slot :name="item.prop" :rows="row" :index="$index">
                            <template v-if="item.prop == 'operator'">
                                <el-link type="primary" style="margin-right: 30px;">下载威胁情报</el-link>
                                <el-link type="primary" :disabled="row.knowledgestatus === '知识抽取完成'" style="margin-right: 30px;">知识抽取</el-link>
                                <el-link type="primary" :disabled="row.knowledgestatus === '知识抽取未完成'" style="margin-right: 30px;">下载知识抽取结果</el-link>
                                <el-link type="primary" :disabled="row.sqlstatus === '已入库' || row.knowledgestatus === '知识抽取未完成'" style="margin-right: 20px;">入库</el-link>
                                <!-- <el-text class="mx-1" type="primary" style="margin-right: 20px;">下载威胁情报</el-text>
                                <el-text class="mx-1" type="primary" style="margin-right: 20px;">知识抽取</el-text>
                                <el-text class="mx-1" type="primary" style="margin-right: 20px;">抽取结果下载</el-text> -->
                                <!-- <el-button type="warning" size="small" :icon="View" @click="viewFunc(row)">
                                    下载
                                </el-button>
                                <el-button type="primary" size="small" :icon="Edit" @click="editFunc(row)">
                                    编辑
                                </el-button>
                                <el-button type="danger" size="small" :icon="Delete" @click="handleDelete(row)">
                                    删除
                                </el-button> -->
                            </template>
                            <span v-else-if="item.formatter">
                                {{ item.formatter(row[item.prop]) }}
                            </span>
                            <span v-else>
                                {{ row[item.prop] }}
                            </span>
                        </slot>
                    </template>
                </el-table-column>
            </template>
        </el-table>
        <el-pagination v-if="hasPagination" :current-page="currentPage" :page-size="10" :background="true"
            :layout="layout" :total="342" @current-change="handleCurrentChange" />
    </div>
</template>

<script setup lang="ts">
import { toRefs, PropType, ref } from 'vue'
import { Delete, Edit, View, Refresh } from '@element-plus/icons-vue';
import { ElMessageBox } from 'element-plus';

const props = defineProps({
    // 表格相关
    tableData: {
        type: Array,
        default: []
    },
    columns: {
        type: Array as PropType<any[]>,
        default: []
    },
    rowKey: {
        type: String,
        default: 'id'
    },
    hasToolbar: {
        type: Boolean,
        default: true
    },
    //  分页相关
    hasPagination: {
        type: Boolean,
        default: true
    },
    total: {
        type: Number,
        default: 0
    },
    currentPage: {
        type: Number,
        default: 1
    },
    pageSize: {
        type: Number,
        default: 10
    },

    layout: {
        type: String,
        default: 'total, prev, pager, next'
    },
    delFunc: {
        type: Function,
        default: () => { }
    },
    viewFunc: {
        type: Function,
        default: () => { }
    },
    editFunc: {
        type: Function,
        default: () => { }
    },
    delSelection: {
        type: Function,
        default: () => { }
    },
    refresh: {
        type: Function,
        default: () => { }
    },
    changePage: {
        type: Function,
        default: () => { }
    }
})

let {
    tableData,
    columns,
    rowKey,
    hasToolbar,
    hasPagination,
    total,
    currentPage,
    pageSize,
    layout,
} = toRefs(props)

columns.value.forEach((item) => {
    if (item.visible === undefined) {
        item.visible = true
    }
})

// 当选择项发生变化时会触发该事件
const multipleSelection = ref([])
const handleSelectionChange = (selection: any[]) => {
    multipleSelection.value = selection
}

// 当前页码变化的事件
const handleCurrentChange = (val: number) => {
    props.changePage(val)
}

const handleDelete = (row) => {
    ElMessageBox.confirm('确定要删除吗？', '提示', {
        type: 'warning'
    })
        .then(async () => {
            props.delFunc(row);
        })
        .catch(() => { });
};

const getIndex = (index: number) => {
    return index + 1 + (currentPage.value - 1) * pageSize.value
}

</script>

<style scoped>
.table-toolbar {
    display: flex;
    justify-content: space-between;
    align-items: flex-end;
    margin-bottom: 10px;
}

.columns-setting-icon {
    display: block;
    font-size: 18px;
    cursor: pointer;
    color: #676767;
}
</style>
<style>
.table-header .cell {
    color: #333;
}
</style>