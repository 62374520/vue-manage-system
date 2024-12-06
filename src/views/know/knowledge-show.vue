<template>
    <div>

        <!-- <TableSearch :query="query" :options="searchOpt" :search="handleSearch" /> -->

        <div class="container">
            <el-form :inline="true" :model="formInline" class="demo-form-inline" label-width="150px">
                <el-row gutter={20} style="margin-left: 0px;">
                    <el-col :span="8">
                        <el-form-item label="头实体类型：">
                            <el-select v-model="formInline.region" placeholder="选择头实体类型" clearable
                                style="width: 200px;">
                                <el-option label="网络威胁情报" value="网络威胁情报" />
                                <el-option label="系统审计日志" value="系统审计日志" />
                            </el-select>
                        </el-form-item>
                    </el-col>
                    <el-col :span="8">
                        <el-form-item label="头实体信息：">
                            <el-input v-model="formInline.user" placeholder="输入头实体信息" clearable />
                        </el-form-item>
                    </el-col>
                    <el-col :span="8">
                        <el-form-item label="关系类型：">
                            <el-select v-model="formInline.region" placeholder="选择关系类型" clearable
                                style="width: 200px;">
                                <el-option label="网络威胁情报" value="网络威胁情报" />
                                <el-option label="系统审计日志" value="系统审计日志" />
                            </el-select>
                        </el-form-item>
                    </el-col>
                    <el-col :span="8">
                        <el-form-item label="尾实体类型：">
                            <el-select v-model="formInline.region" placeholder="选择尾实体类型" clearable
                                style="width: 200px;">
                                <el-option label="网络威胁情报" value="网络威胁情报" />
                                <el-option label="系统审计日志" value="系统审计日志" />
                            </el-select>
                        </el-form-item>
                    </el-col>
                    <el-col :span="8">
                        <el-form-item label="尾实体信息：">
                            <el-input v-model="formInline.user" placeholder="输入尾实体信息" clearable />
                        </el-form-item>
                    </el-col>
                    <el-col :span="8" style="padding-left: 120px;">
                        <el-form-item>
                            <el-button type="primary" :icon="Search">查询</el-button>
                            <el-button type="primary" :icon="Refresh" style="margin-left: 40px;">图谱还原</el-button>
                        </el-form-item>
                    </el-col>
                </el-row>
            </el-form>
            <div class="image-container">
                <div class="controls">
                    <span>缩放比例：{{ Math.round(scale * 100) }}%</span>
                    <el-slider v-model="scale" :min="0.1" :max="10" step="0.1" style="width: 300px; margin-left: 10px;"
                        show-tooltip />
                </div>

                <!-- 矩形框 -->
                <div class="image-box">
                    <div class="image-wrapper" :style="{
                        transform: `scale(${scale}) translate(${translateX}px, ${translateY}px)`,
                        transformOrigin: 'top left'
                    }" @mousedown="startDrag" @mousemove="onDrag" @mouseup="endDrag" @mouseleave="endDrag">
                        <el-image :src="imageSrc" fit="contain" style="width: 100%; height: 100%;" />
                    </div>
                </div>
            </div>

        </div>

        <el-dialog :title="isEdit ? '编辑' : '新增'" v-model="visible" width="700px" destroy-on-close
            :close-on-click-modal="false" @close="closeDialog">
            <TableEdit :form-data="rowData" :options="options" :edit="isEdit" :update="updateData" />
        </el-dialog>
        <el-dialog title="查看详情" v-model="visible1" width="700px" destroy-on-close>
            <TableDetail :data="viewData"></TableDetail>
        </el-dialog>
    </div>
    <!-- <div class="demo-image__placeholder">
    <div class="block">
      <el-image :src="imageSrc" />
    </div>
  </div> -->

    <!-- <div class="svg-container">
    <div class="block">
      <el-image
        :src="imageSrc"
        style="max-width: 100%;"
        :style="{ transform: `scale(${scale})`, transformOrigin: 'top left' }"
      />
    </div>
    <div class="controls">
      <span>缩放比例：{{ Math.round(scale * 100) }}%</span>
      <el-slider
        v-model="scale"
        :min="0.1"
        :max="5"
        step="0.1"
        style="width: 300px; margin-left: 10px;"
        show-tooltip
      />
    </div>
  </div> -->

</template>

<script setup lang="ts" name="system-user">
import { ref, reactive } from 'vue';
import { ElMessage } from 'element-plus';
import { CirclePlusFilled, Search, Refresh } from '@element-plus/icons-vue';
import { User } from '@/types/user';
import { fetchUserData } from '@/api';
import TableCustom from '@/components/table-custom.vue';
import TableDetail from '@/components/table-detail.vue';
import TableSearch from '@/components/table-search.vue';
import { FormOption, FormOptionList } from '@/types/form-option';
import imageSrc from '../../assets/img/know.svg';


const scale = ref(1); // 初始缩放比例
const translateX = ref(0); // X轴平移
const translateY = ref(0); // Y轴平移
const isDragging = ref(false); // 是否正在拖动
let startX = 0;
let startY = 0;

const srcList = [
    'https://fuss10.elemecdn.com/a/3f/3302e58f9a181d2509f3dc0fa68b0jpeg.jpeg',
    'https://fuss10.elemecdn.com/1/34/19aa98b1fcb2781c4fba33d850549jpeg.jpeg',
    'https://fuss10.elemecdn.com/0/6f/e35ff375812e6b0020b6b4e8f9583jpeg.jpeg',
    'https://fuss10.elemecdn.com/9/bb/e27858e973f5d7d3904835f46abbdjpeg.jpeg',
    'https://fuss10.elemecdn.com/d/e6/c4d93a3805b3ce3f323f7974e6f78jpeg.jpeg',
    'https://fuss10.elemecdn.com/3/28/bbf893f792f03a54408b3b7a7ebf0jpeg.jpeg',
    'https://fuss10.elemecdn.com/2/11/6535bcfb26e4c79b48ddde44f4b6fjpeg.jpeg',
]

const startDrag = (event) => {
    isDragging.value = true;
    startX = event.clientX - translateX.value;
    startY = event.clientY - translateY.value;
    event.preventDefault(); // 防止页面滚动
};

// 拖动中
const onDrag = (event) => {
    if (isDragging.value) {
        translateX.value = event.clientX - startX;
        translateY.value = event.clientY - startY;
    }
};

// 结束拖动
const endDrag = () => {
    isDragging.value = false;
};


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
    { prop: 'filename', label: '文件名', width: 350, align: 'center' },
    { prop: 'filetype', label: '文件类型' },
    // { prop: 'datatype', label: '数据类型' },
    { prop: 'uploadtime', label: '上传时间' },
    { prop: 'status', label: '上传状态' },
    { prop: 'operator', label: '操作', width: 250 },
])
const page = reactive({
    index: 1,
    size: 10,
    total: 0,
})
const tableData = ref<User[]>([]);

const formInline = reactive({
    user: '',
    region: '',
    date: '',
    date1: '',
    date2: '',
})

interface File {
    filename: string
    filetype: string
    datatype: string
    uploadtime: string
    status: string
}
const fileData: File[] = [
    {
        filename: 'Volt Typhoon targets US critical infrastructure with living-off-the-land techniques _ Microsoft Security Blog.pdf',
        filetype: '网络威胁情报',
        datatype: '2024-12-01 14:25:56',
        uploadtime: '2024-12-01 14:25:56',
        status: '上传成功',
    },
    {
        filename: 'APT29 attacks Embassies using CVE-2023-38831 - report en.pdf',
        filetype: '网络威胁情报',
        datatype: 'Lohrbergstr. 86c, Süd Lilli, Saarland',
        uploadtime: '2024-12-01 14:25:57',
        status: '上传成功',
    },
    {
        filename: 'WildCard_ The APT Behind SysJoker Targets Critical Sectors in Israel.pdf',
        filetype: '网络威胁情报',
        datatype: 'Lohrbergstr. 86c, Süd Lilli, Saarland',
        uploadtime: '2024-12-01 14:26:02',
        status: '上传成功',
    },
    {
        filename: 'ta1-cadets-e3.json',
        filetype: '系统审计日志',
        datatype: 'Lohrbergstr. 86c, Süd Lilli, Saarland',
        uploadtime: '2024-12-01 14:26:16',
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

<style scoped>
/* .svg-container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.controls {
  margin-top: 20px;
  display: flex;
  align-items: center;
} */

/* 容器设置 */
.image-container {
    display: flex;
    flex-direction: column;
    align-items: center;
}

/* 控制区样式 */
.controls {
    margin-top: 20px;
    display: flex;
    align-items: center;
}

/* 矩形框 */
.image-box {
    width: 100%;
    /* 设置矩形框的宽度 */
    height: 400px;
    /* 设置矩形框的高度 */
    border: 1px solid #ddd;
    /* 边框样式 */
    overflow: hidden;
    /* 隐藏超出部分 */
    position: relative;
    /* 保证子元素相对定位 */
}

/* 图片容器 */
.image-wrapper {
    width: 100%;
    height: 100%;
    cursor: grab;
}
</style>