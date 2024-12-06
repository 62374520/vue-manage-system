<template>
    <div>
        <el-tabs v-model="activeName" class="demo-tabs" @tab-click="handleClick">
            <el-tab-pane label="实体管理" name="first">
                <div class="container">
                    <TableCustom :columns="columns" :tableData="entityData" :total="page.total" :viewFunc="handleView"
                        :delFunc="handleDelete" :page-change="changePage" :editFunc="handleEdit">
                        <template #toolbarBtn>
                            <el-form :inline="true" :model="formInline" class="demo-form-inline" label-width="150px">
                                <el-row gutter={20} style="margin-left: 0px;">
                                    <el-col :span="12">
                                        <el-form-item label="实体类型：">
                                            <el-select v-model="formInline.region" placeholder="选择实体类型" clearable
                                                style="width: 200px;">
                                                <el-option label="网络威胁情报" value="网络威胁情报" />
                                                <el-option label="系统审计日志" value="系统审计日志" />
                                            </el-select>
                                        </el-form-item>
                                    </el-col>
                                    <el-col :span="12">
                                        <el-form-item label="创建时间区间：">
                                            <el-col :span="11">
                                                <el-date-picker v-model="formInline.date1" type="date"
                                                    placeholder="选择开始时间" style="width: 100%" />
                                            </el-col>
                                            <el-col :span="2" style="text-align: center; line-height: 36px;">
                                                <span>-</span>
                                            </el-col>
                                            <el-col :span="11">
                                                <el-date-picker v-model="formInline.date2" placeholder="选择结束时间"
                                                    style="width: 100%" />
                                            </el-col>
                                        </el-form-item>
                                    </el-col>
                                    <el-col :span="12">
                                        <el-form-item label="实体信息：">
                                            <el-input v-model="formInline.user" placeholder="输入实体信息" clearable />
                                        </el-form-item>
                                    </el-col>
                                    <el-col :span="12">
                                        <el-form-item label="更新时间区间：">
                                            <el-col :span="11">
                                                <el-date-picker v-model="formInline.date1" type="date"
                                                    placeholder="选择开始时间" style="width: 100%" />
                                            </el-col>
                                            <el-col :span="2" style="text-align: center; line-height: 36px;">
                                                <span>-</span>
                                            </el-col>
                                            <el-col :span="11">
                                                <el-date-picker v-model="formInline.date2" placeholder="选择结束时间"
                                                    style="width: 100%" />
                                            </el-col>
                                        </el-form-item>
                                    </el-col>
                                    <el-col :span="12"></el-col>
                                    <el-col :span="12" style="padding-left: 1286px;">
                                        <el-form-item>
                                            <el-button type="primary" :icon="Search">查询</el-button>
                                        </el-form-item>
                                    </el-col>
                                </el-row>
                            </el-form>

                        </template>
                    </TableCustom>

                </div>
            </el-tab-pane>
            <el-tab-pane label="关系管理" name="second">关系管理</el-tab-pane>
        </el-tabs>

        <!-- <TableSearch :query="query" :options="searchOpt" :search="handleSearch" /> -->



        <el-dialog :title="isEdit ? '编辑' : '新增'" v-model="visible" width="700px" destroy-on-close
            :close-on-click-modal="false" @close="closeDialog">
            <TableEdit :form-data="rowData" :options="options" :edit="isEdit" :update="updateData" />
        </el-dialog>
        <el-dialog title="查看详情" v-model="visible1" width="700px" destroy-on-close>
            <TableDetail :data="viewData"></TableDetail>
        </el-dialog>
    </div>
</template>

<script setup lang="ts" name="system-user">
import { ref, reactive } from 'vue';
import { ElMessage } from 'element-plus';
import { CirclePlusFilled, Search, Refresh } from '@element-plus/icons-vue';
import type { TabsPaneContext } from 'element-plus'
import { User } from '@/types/user';
import { fetchUserData } from '@/api';
import TableCustom from '@/components/table-custom-entity.vue';
import TableDetail from '@/components/table-detail.vue';
import TableSearch from '@/components/table-search.vue';
import { FormOption, FormOptionList } from '@/types/form-option';


const activeName = ref('first')
const handleClick = (tab: TabsPaneContext, event: Event) => {
    console.log(tab, event)
}

// 查询相关
const query = reactive({
    name: '',
});
const searchOpt = ref<FormOptionList[]>([
    { type: 'input', label: '用户名：', prop: 'name' }
])
const handleSearch = () => {
    changePage(1);
};

// 表格相关
let columns = ref([
    { prop: 'id', label: '实体id', width: 100, align: 'center' },
    { prop: 'name', label: '实体名称', width: 250, align: 'center' },
    // { prop: 'datatype', label: '数据类型' },
    { prop: 'type', label: '实体类型', width: 200, align: 'center' },
    { prop: 'createtime', label: '创建时间', width: 300, align: 'center' },
    { prop: 'updatetime', label: '更新时间', width: 300, align: 'center' },
    { prop: 'operator', label: '操作' },
])
const page = reactive({
    index: 1,
    size: 10,
    total: 0,
})

const formInline = reactive({
    user: '',
    region: '',
    date: '',
    date1: '',
    date2: '',
})

const onSubmit = () => {
    console.log('submit!')
}

const radio1 = ref('网络威胁情报')
const textarea = ref('')
const dialogFormVisible = ref(false)
const formLabelWidth = '140px'

const form = reactive({
    name: '',
    region: '',
    date1: '',
    date2: '',
    delivery: false,
    type: [],
    resource: '',
    desc: '',
})

const tableData = ref<User[]>([]);

interface Entity {
    id: string
    name: string
    type: string
    createtime: string
    updatetime: string
}

const entityData: Entity[] = [
    {
        id: '1',
        name: 'APT39',
        type: 'Attacker',
        createtime: '2024-10-31 09:43:33',
        updatetime: '2024-10-31 09:43:33',
    },
    {
        id: '1',
        name: 'CVE-2018-1999036',
        type: 'Vulnerability',
        createtime: '2024-10-31 09:43:31',
        updatetime: '2024-10-31 09:43:31',
    },
    {
        id: '2',
        name: 'using Malicious Files',
        type: 'AttackType',
        createtime: '2024-10-31 09:43:29',
        updatetime: '2024-10-31 09:43:29',
    },
    {
        id: '3',
        name: 'jenkins',
        type: 'Software',
        createtime: '2024-10-31 09:43:27',
        updatetime: '2024-10-31 09:43:27',
    },
    {
        id: '4',
        name: 'Improper Authorizationc',
        type: 'Weakness',
        createtime: '2024-10-31 09:43:31',
        updatetime: '2024-10-31 09:43:31',
    },
    {
        id: '5',
        name: 'Insertion of Sensitive Information into Externally-Accessible File or Directory',
        type: 'Weakness',
        createtime: '2024-10-31 09:43:22',
        updatetime: '2024-10-31 09:43:22',
    },
    {
        id: '6',
        name: 'Man-in-the-Middle Attackc',
        type: 'AttackType',
        createtime: '2024-10-30 21:27:24',
        updatetime: '2024-10-30 21:27:24',
    },
    {
        id: '7',
        name: 'Windows 10',
        type: 'OS',
        createtime: '2024-10-30 21:27:22',
        updatetime: '2024-10-30 21:27:22',
    },
    {
        id: '8',
        name: 'Microsoft Exchange',
        type: 'Software',
        createtime: '2024-10-30 21:27:13',
        updatetime: '2024-10-30 21:27:13',
    },
    {
        id: '9',
        name: 'IOSTriangulation',
        type: 'AttackCampaign',
        createtime: '2024-10-30 21:27:02',
        updatetime: '2024-10-30 21:27:02',
    },
    {
        id: '10',
        name: 'CVE-2023-4966',
        type: 'Vulnerability',
        createtime: '2024-10-31 13:47:13',
        updatetime: '2024-10-31 13:47:13',
    },
]
const getData = async () => {
    const res = await fetchUserData()
    tableData.value = res.data.list;
    page.total = res.data.pageTotal;
};
getData();

const changePage = (val: number) => {
    page.index = val;
    getData();
};

// 新增/编辑弹窗相关
let options = ref<FormOption>({
    labelWidth: '100px',
    span: 12,
    list: [
        { type: 'input', label: '用户名', prop: 'name', required: true },
        { type: 'input', label: '手机号', prop: 'phone', required: true },
        { type: 'input', label: '密码', prop: 'password', required: true },
        { type: 'input', label: '邮箱', prop: 'email', required: true },
        { type: 'input', label: '角色', prop: 'role', required: true },
    ]
})
const visible = ref(false);
const isEdit = ref(false);
const rowData = ref({});
const handleEdit = (row: User) => {
    rowData.value = { ...row };
    isEdit.value = true;
    visible.value = true;
};
const updateData = () => {
    closeDialog();
    getData();
};

const closeDialog = () => {
    visible.value = false;
    isEdit.value = false;
};

// 查看详情弹窗相关
const visible1 = ref(false);
const viewData = ref({
    row: {},
    list: []
});
const handleView = (row: User) => {
    viewData.value.row = { ...row }
    viewData.value.list = [
        {
            prop: 'id',
            label: '用户ID',
        },
        {
            prop: 'name',
            label: '用户名',
        },
        {
            prop: 'password',
            label: '密码',
        },
        {
            prop: 'email',
            label: '邮箱',
        },
        {
            prop: 'phone',
            label: '电话',
        },
        {
            prop: 'role',
            label: '角色',
        },
        {
            prop: 'date',
            label: '注册日期',
        },
    ]
    visible1.value = true;
};

// 删除相关
const handleDelete = (row: User) => {
    ElMessage.success('删除成功');
}
</script>

<style scoped>
.demo-tabs>.el-tabs__content {
    padding: 32px;
    color: #6b778c;
    font-size: 32px;
    font-weight: 600;
}
</style>