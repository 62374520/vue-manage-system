<template>
    <div>
        
        <TableSearch :query="query" :options="searchOpt" :search="handleSearch" />
         
        <div class="container">
            <TableCustom :columns="columns" :tableData="fileData" :total="page.total" :viewFunc="handleView"
                :delFunc="handleDelete" :page-change="changePage" :editFunc="handleEdit">
                <template #toolbarBtn>
                    <!-- <el-button type="warning" :icon="CirclePlusFilled" @click="visible = true">新增</el-button> -->
                    <!-- <el-button type="primary" style="margin-right: 10px;"><el-icon class="el-icon--right"><UploadFilled /></el-icon>上传文件</el-button>   
                    <el-button type="primary" style="margin-right: 10px;"><el-icon class="el-icon--right"><DeleteFilled /></el-icon>删除文件</el-button>   
                    <el-button type="primary" style="margin-right: 10px;"><el-icon class="el-icon--right"><Refresh /></el-icon>刷新</el-button>    -->
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
    <!-- <el-upload
      class="upload-demo"
      drag
      action="https://run.mocky.io/v3/9d059bf9-4660-45f2-925d-ce80ad6c4d15"
      multiple
    >
      <el-icon class="el-icon--upload"><upload-filled /></el-icon>
      <div class="el-upload__text">
        Drop file here or <em>click to upload</em>
      </div>
      <template #tip>
        <div class="el-upload__tip">
          请上传结构化网络威胁情报
        </div>
      </template>
    </el-upload> -->
</template>

<script setup lang="ts" name="system-user">
import { ref, reactive } from 'vue';
import { ElMessage } from 'element-plus';
import { CirclePlusFilled } from '@element-plus/icons-vue';
import { User } from '@/types/user';
import { fetchUserData } from '@/api';
import TableCustom from '@/components/table-custom.vue';
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
    { prop: 'filename', label: '文件名', width: 350, align: 'center'  },
    { prop: 'filetype', label: '文件类型', width:120 },
    // { prop: 'datatype', label: '数据类型' },
    { prop: 'uploadtime', label: '上传时间' },
    { prop: 'status', label: '上传状态', width:100 },
    { prop: 'yuchuli', label: '预处理状态', width:150 },
    { prop: 'operator', label: '操作', width: 300 },
])
const page = reactive({
    index: 1,
    size: 10,
    total: 0,
})
const tableData = ref<User[]>([]);

interface File {
    filename: string
    filetype: string
    datatype: string
    uploadtime: string
    status: string
    yuchuli:string
}
const fileData: File[] = [
  {
    filename: 'Volt Typhoon targets US critical infrastructure with living-off-the-land techniques _ Microsoft Security Blog.pdf',
    filetype: '网络威胁情报',
    datatype: '2024-12-01 14:25:56',
    uploadtime: '2024-12-01 14:25:56',
    status: '上传成功',
    yuchuli:'预处理未完成',
  },
  {
    filename: 'APT29 attacks Embassies using CVE-2023-38831 - report en.pdf',
    filetype: '网络威胁情报',
    datatype: 'Lohrbergstr. 86c, Süd Lilli, Saarland',
    uploadtime: '2024-12-01 14:25:57',
    status: '上传成功',
    yuchuli:'预处理未完成',
  },
  {
    filename: 'WildCard_ The APT Behind SysJoker Targets Critical Sectors in Israel.pdf',
    filetype: '网络威胁情报',
    datatype: 'Lohrbergstr. 86c, Süd Lilli, Saarland',
    uploadtime: '2024-12-01 14:26:02',
    status: '上传成功',
    yuchuli:'预处理未完成',
  },
  {
    filename: 'ta1-cadets-e3.json',
    filetype: '系统审计日志',
    datatype: 'Lohrbergstr. 86c, Süd Lilli, Saarland',
    uploadtime: '2024-12-01 14:26:16',
    status: '上传成功',
    yuchuli:'预处理未完成',
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