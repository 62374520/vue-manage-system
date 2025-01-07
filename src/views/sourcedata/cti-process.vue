<template>
    <div>

        <!-- <TableSearch :query="query" :options="searchOpt" :search="handleSearch" /> -->

        <div class="container">
            <TableCustom :columns="columns" :tableData="fileData" :total="page.total" :viewFunc="handleView"
                :delFunc="handleDelete" :page-change="changePage" :editFunc="handleEdit">
                <template #toolbarBtn>
                    <el-form :inline="true" :model="formInline" class="demo-form-inline" label-width="150px">
                        <el-row gutter={40} style="margin-left: 300px;">
                            <el-col :span="12">
                                <el-form-item label="网络威胁情报名称：">
                                    <el-input v-model="formInline.user" placeholder="请输入网络威胁情报名称" clearable />
                                </el-form-item>
                            </el-col>
                            <el-col :span="12">
                                <el-form-item label="上传时间区间：">
                                    <el-col :span="11">
                                        <el-date-picker v-model="formInline.date1" type="date" placeholder="选择开始时间"
                                            style="width: 100%" />
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
                        </el-row>
                        <el-row gutter={20} style="margin-left: 300px;">
                            <el-col :span="12">
                                <el-form-item label="知识抽取状态：">
                                    <el-select v-model="formInline.region" placeholder="选择知识抽取状态" clearable
                                        style="width: 200px;">
                                        <el-option label="网络威胁情报" value="网络威胁情报" />
                                        <el-option label="系统审计日志" value="系统审计日志" />
                                    </el-select>
                                </el-form-item>
                            </el-col>
                            <el-col :span="12">
                                <el-form-item label="发布时间区间：">
                                    <el-col :span="11">
                                        <el-date-picker v-model="formInline.date1" type="date" placeholder="选择开始时间"
                                            style="width: 100%" />
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
                        </el-row>
                        <el-row gutter={20} style="margin-left: 300px;">
                            <el-col :span="12">
                                <el-form-item label="知识融合状态：">
                                    <el-select v-model="formInline.region" placeholder="选择知识融合状态" clearable
                                        style="width: 200px;">
                                        <el-option label="网络威胁情报" value="网络威胁情报" />
                                        <el-option label="系统审计日志" value="系统审计日志" />
                                    </el-select>
                                </el-form-item>
                            </el-col>
                            <el-col :span="12" style="text-align: right;">
                                <el-form-item>
                                    <el-button type="primary" :icon="Search"
                                        @click="dialogFormVisible = true">查询</el-button>
                                    <!-- <el-button type="primary" @click="onSubmit">查询</el-button> -->
                                </el-form-item>
                            </el-col>
                        </el-row>
                    </el-form>
                    <!-- <el-button type="warning" :icon="CirclePlusFilled" @click="visible = true">新增</el-button> -->
                    <el-dialog v-model="dialogFormVisible" title="知识抽取任务" width="600">
                        <el-form :model="form">
                            <el-form-item label="网络威胁情报名称" :label-width="formLabelWidth">
                                <el-col :span="24" style="text-align: center;">
                                    <el-text class="mx-1">Volt Typhoon targets US critical infrastructure
                                        with...</el-text>
                                </el-col>
                            </el-form-item>
                            <el-form-item label="当前状态" :label-width="formLabelWidth">
                                <el-col :span="24" style="text-align: center;">
                                    <el-text class="mx-1">知识抽取未完成</el-text>
                                </el-col>
                            </el-form-item>
                            <el-form-item label="选择抽取模式" :label-width="formLabelWidth">
                                <el-col :span="24" style="text-align: center;">
                                    <el-select v-model="formInline.date" placeholder="选择抽取模式" clearable
                                        style="width: 350px;">
                                        <el-option label="实体识别" value="实体识别" />
                                        <el-option label="关系抽取" value="关系抽取" />
                                        <el-option label="知识三元组抽取" value="知识三元组抽取" />
                                    </el-select>
                                </el-col>
                            </el-form-item>
                            <el-form-item label="选择实体识别模型" :label-width="formLabelWidth">
                                <el-col :span="24" style="text-align: center;">
                                    <el-select v-model="formInline.date1" placeholder="选择抽取模型" clearable
                                        style="width: 350px;">
                                        <el-option label="SecBABC" value="SecBABC" />
                                    </el-select>
                                </el-col>
                            </el-form-item>
                            <el-form-item label="模型参数" :label-width="formLabelWidth">
                                <el-col :span="24" style="text-align: center;">
                                    <el-select  v-model="formInline.date2" placeholder="默认参数" clearable
                                        style="width: 350px; " >
                                        <el-option label="默认参数" value="默认参数" />
                                    </el-select>
                                </el-col>
                            </el-form-item>
                        </el-form>
                        <template #footer>
                            <div class="dialog-footer">
                                <el-button @click="dialogFormVisible = false">取消</el-button>
                                <el-button type="primary" @click="dialogFormVisible = false">
                                    开始抽取
                                </el-button>
                            </div>
                        </template>
                    </el-dialog>
                </template>
            </TableCustom>

        </div>

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
import { CirclePlusFilled, Search } from '@element-plus/icons-vue';
import { User } from '@/types/user';
import { fetchUserData } from '@/api';
import TableCustom from '@/components/table-custom-cti.vue';
import TableDetail from '@/components/table-detail.vue';
import TableSearch from '@/components/table-search.vue';
import { FormOption, FormOptionList } from '@/types/form-option';

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
    { prop: 'filename', label: '网络威胁情报名称', width: 350, align: 'center' },
    { prop: 'releasetime', label: '发布时间', width: 200, align: 'center' },
    // { prop: 'datatype', label: '数据类型' },
    { prop: 'uploadtime', label: '上传时间', width: 200, align: 'center' },
    { prop: 'knowledgestatus', label: '知识抽取状态', width: 150, align: 'center' },
    { prop: 'sqlstatus', label: '知识融合状态', width: 150, align: 'center' },
    // { prop: 'sqlstatus', label: '存储状态', width: 150, align: 'center' },
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

interface File {
    filename: string
    datatype: string
    uploadtime: string
    releasetime: string
    sqlstatus: string
    knowledgestatus: string
    status: string
}

const fileData: File[] = [
    {
        filename: 'WildCard_ The APT Behind SysJoker Targets Critical Sectors in Israel.pdf',
        releasetime: '2023-11-27',
        datatype: 'Lohrbergstr. 86c, Süd Lilli, Saarland',
        uploadtime: '2024-12-01 14:26:02',
        knowledgestatus: '知识抽取未完成',
        // sqlstatus: '未入库',
        sqlstatus: '知识融合未完成',
        status: '上传成功',
    },
    {
        filename: 'Volt Typhoon targets US critical infrastructure with living-off-the-land techniques _ Microsoft Security Blog.pdf',
        releasetime: '2023-05-24',
        datatype: '2024-12-01 14:25:56',
        uploadtime: '2024-12-01 14:25:56',
        knowledgestatus: '知识抽取未完成',
        // sqlstatus: '未入库',
        sqlstatus: '知识融合未完成',
        status: '上传成功',
    },
    {
        filename: 'APT29 attacks Embassies using CVE-2023-38831 - report en.pdf',
        releasetime: '2023-11-14',
        datatype: 'Lohrbergstr. 86c, Süd Lilli, Saarland',
        uploadtime: '2024-12-01 14:25:57',
        knowledgestatus: '知识抽取完成',
        // sqlstatus: '未入库',
        sqlstatus: '知识融合未完成',
        status: '上传成功',
    },
    {
        filename: 'Welcome to Goot Camp_ Tracking the Evolution of GOOTLOADER Operations _ Mandiant.pdf',
        releasetime: '2023-01-26',
        datatype: 'Lohrbergstr. 86c, Süd Lilli, Saarland',
        uploadtime: '2024-11-30 08:34:02',
        knowledgestatus: '知识抽取完成',
        // sqlstatus: '已入库',
        sqlstatus: '知识融合完成',
        status: '上传成功',
    },
    {
        filename: 'Internet Explorer 0-day exploited by North Korean actor APT37.pdf',
        releasetime: '2022-12-07',
        datatype: 'Lohrbergstr. 86c, Süd Lilli, Saarland',
        uploadtime: '2024-11-29 14:25:02',
        knowledgestatus: '知识抽取完成',
        // sqlstatus: '已入库',
        sqlstatus: '知识融合完成',
        status: '上传成功',
    },
    {
        filename: 'Blowing Cobalt Strike Out of the Water With Memory Analysis.pdf',
        releasetime: '2022-12-02',
        datatype: 'Lohrbergstr. 86c, Süd Lilli, Saarland',
        uploadtime: '2024-11-29 14:24:59',
        knowledgestatus: '知识抽取完成',
        // sqlstatus: '已入库',
        sqlstatus: '知识融合完成',
        status: '上传成功',
    },
    {
        filename: 'RomCom Threat Actor Abuses KeePass and SolarWinds to Target Ukraine and Potentially the United Kingdom.pdf',
        releasetime: '2022-11-02',
        datatype: 'Lohrbergstr. 86c, Süd Lilli, Saarland',
        uploadtime: '2024-11-29 14:24:58',
        knowledgestatus: '知识抽取完成',
        // sqlstatus: '已入库',
        sqlstatus: '知识融合完成',
        status: '上传成功',
    },
    {
        filename: 'IOCs-the-rise-of-earth-aughisky-tracking-the-campaigns-taidoor-started.pdf',
        releasetime: '2022-10-04',
        datatype: 'Lohrbergstr. 86c, Süd Lilli, Saarland',
        uploadtime: '2024-11-27 11:39:56',
        knowledgestatus: '知识抽取完成',
        // sqlstatus: '已入库',
        sqlstatus: '知识融合完成',
        status: '上传成功',
    },
    {
        filename: 'Cisco Talos Intelligence Group - Comprehensive Threat Intelligence_ Bitter APT adds Bangladesh to their targets.pdf',
        releasetime: '2022-05-11',
        datatype: 'Lohrbergstr. 86c, Süd Lilli, Saarland',
        uploadtime: '2024-11-27 11:39:55',
        knowledgestatus: '知识抽取完成',
        // sqlstatus: '已入库',
        sqlstatus: '知识融合完成',
        status: '上传成功',
    },
    {
        filename: 'Cisco Talos Intelligence Group - Comprehensive Threat Intelligence_ Mustang Panda deploys a new wave of malware targeting Europe.pdf',
        releasetime: '2022-05-05',
        datatype: 'Lohrbergstr. 86c, Süd Lilli, Saarland',
        uploadtime: '2024-11-27 11:39:50',
        knowledgestatus: '知识抽取完成',
        // sqlstatus: '已入库',
        sqlstatus: '知识融合完成',
        status: '上传成功',
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

<style scoped></style>