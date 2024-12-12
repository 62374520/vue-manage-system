<template>
    任务名称，选择文件，攻击知识提取，检测模型选择，定时任务（任务1，开始时间，结束时间，任务状态），攻击检测，检测结果（选中数据集包含节点数量，边数量，使用的攻击知识，存在攻击行为数量），告警
    <div>
        <div class="search-container">
            <el-form ref="searchRef" :inline="true">
                <el-form-item>
                    <el-button type="primary" @click="dialogFormVisible = true" style="margin-right: 10px;"><el-icon
                            class="el-icon--right">
                            <UploadFilled />
                        </el-icon>上传文件</el-button>
                </el-form-item>
                <TableCustom :columns="columns" :tableData="detectData" :total="page.total" :viewFunc="handleView"
                        :page-change="changePage">
                        <template #toolbarBtn>
                            
                        </template>
                    </TableCustom>
            </el-form>
            <el-dialog v-model="dialogFormVisible" title="新增攻击检测任务" width="600">
                <el-form-item label="任务名称：" :label-width="formLabelWidth">
                    <el-col :span="24" style="text-align: center;">
                        <el-input v-model="formInline.date7" style="width: 82%" placeholder="输入任务名称" />
                    </el-col>
                </el-form-item>
                <el-form-item label="任务描述：" :label-width="formLabelWidth">
                    <el-col :span="24" style="text-align: center;">
                        <el-input v-model="formInline.date6" style="width: 82%" placeholder="输入任务描述" />
                    </el-col>
                </el-form-item>
                <el-form :model="form">
                    <el-form-item label="选择系统溯源日志：" :label-width="formLabelWidth">
                        <el-col :span="24" style="text-align: center;">
                            <el-select v-model="formInline.date" placeholder="选择系统溯源日志" clearable style="width: 350px;">
                                <el-option label="ta1-cadets-e3" value="ta1-cadets-e3" />
                            </el-select>
                        </el-col>
                    </el-form-item>
                    <el-form-item label="选择检测模型：" :label-width="formLabelWidth">
                        <el-col :span="24" style="text-align: center;">
                            <el-select v-model="formInline.date2" placeholder="选择检测模型：" clearable style="width: 350px;">
                                <el-option label="KSAD攻击检测模型" value="KSAD攻击检测模型" />
                            </el-select>
                        </el-col>
                    </el-form-item>
                    <el-form-item label="选择模型参数：" :label-width="formLabelWidth">
                        <el-col :span="24" style="text-align: center;">
                            <el-select disabled v-model="formInline.date3" placeholder="默认参数" clearable
                                style="width: 350px; ">
                                <el-option label="默认参数" value="默认参数" />
                            </el-select>
                        </el-col>
                    </el-form-item>
                    <el-form-item label="是否使用攻击知识" :label-width="formLabelWidth">
                        <el-col :span="24" style="text-align: center;">
                            <el-radio-group v-model="formInline.date4">
                                <el-radio value="1" size="large" border>使用</el-radio>
                                <el-radio value="2" size="large" border>不使用</el-radio>
                            </el-radio-group>
                        </el-col>
                    </el-form-item>

                </el-form>
                <template #footer>
                    <div class="dialog-footer">
                        <el-button @click="dialogFormVisible = false">取消</el-button>
                        <el-button type="primary" @click="dialogFormVisible = false">
                            开始任务
                        </el-button>
                    </div>
                </template>
            </el-dialog>
        </div>

        <el-row :gutter="20" class="mgb20" style="margin-top: 100px;">
            <!-- <el-col :span="12">
                <el-card shadow="hover">
                    <div class="card-header">
                        <p class="card-header-title">订单动态</p>
                        <p class="card-header-desc">最近一周订单状态，包括订单成交量和订单退货量</p>
                    </div>
                    <v-chart class="chart" :option="dashOpt1" />
                </el-card>
            </el-col> -->
            <el-col :span="12">
                <el-card shadow="hover">
                    <el-row :gutter="20" class="mgb20">
                        <el-col :span="24">
                            <p class="card-header-title flex-align">
                                <el-icon :size="20" class="icon" style="color:red">
                                    <WarnTriangleFilled />
                                </el-icon>
                            <p class="card-header-title">攻击检测结果</p>
                            </p>
                            <p class="card-header-desc">当前任务的攻击检测结果</p>
                        </el-col>
                    </el-row>
                    <el-row :gutter="20" class="mgb20">
                        <el-col :span="12">
                            <el-card shadow="hover" body-class="card-body">
                                <el-icon class="card-icon bg1">
                                    <StarFilled />
                                </el-icon>
                                <div class="card-content">
                                    <div><el-text class="mx-1" style="font-weight: bold;" size="large">实体数量统计</el-text>
                                    </div>
                                    <countup class="card-num color1" :end="1627035" />
                                    <div><el-text class="mx-1">当前任务的溯源实体记录数</el-text></div>
                                </div>
                            </el-card>
                        </el-col>
                        <el-col :span="12">
                            <el-card shadow="hover" body-class="card-body">
                                <el-icon class="card-icon bg2">
                                    <Share />
                                </el-icon>
                                <div class="card-content">
                                    <div><el-text class="mx-1" style="font-weight: bold;" size="large">边数量统计</el-text>
                                    </div>
                                    <countup class="card-num color2" :end="3303264" />
                                    <div><el-text class="mx-1">当前任务的溯源边记录数</el-text></div>
                                </div>
                            </el-card>
                        </el-col>
                    </el-row>
                    <el-row :gutter="20" class="mgb20">
                        <el-col :span="12">
                            <el-card shadow="hover" body-class="card-body">
                                <el-icon class="card-icon bg3">
                                    <Document />
                                </el-icon>
                                <div class="card-content">
                                    <div><el-text class="mx-1" style="font-weight: bold;"
                                            size="large">攻击知识信息统计</el-text></div>
                                    <countup class="card-num color3" :end="352" />
                                    <div><el-text class="mx-1">生成攻击知识的网络威胁情报数量</el-text></div>
                                </div>
                            </el-card>
                        </el-col>
                        <el-col :span="12">
                            <el-card shadow="hover" body-class="card-body">
                                <el-icon class="card-icon bg4">
                                    <CloseBold />
                                </el-icon>
                                <div class="card-content">
                                    <div><el-text class="mx-1" style="font-weight: bold;"
                                            size="large">异常攻击实体统计</el-text></div>
                                    <countup class="card-num color4" :end="12808" />
                                    <div><el-text class="mx-1">当前任务检测出的攻击实体数量</el-text></div>
                                </div>
                            </el-card>
                        </el-col>
                    </el-row>
                </el-card>
            </el-col>
        </el-row>
        <!-- <el-row :gutter="20">
            <el-col :span="7">
                <el-card shadow="hover" :body-style="{ height: '400px' }">
                    <div class="card-header">
                        <p class="card-header-title">时间线</p>
                        <p class="card-header-desc">最新的销售动态和活动信息</p>
                    </div>
                    <el-timeline>
                        <el-timeline-item v-for="(activity, index) in activities" :key="index" :color="activity.color">
                            <div class="timeline-item">
                                <div>
                                    <p>{{ activity.content }}</p>
                                    <p class="timeline-desc">{{ activity.description }}</p>
                                </div>
                                <div class="timeline-time">{{ activity.timestamp }}</div>
                            </div>
                        </el-timeline-item>
                    </el-timeline>
                </el-card>
            </el-col>
            <el-col :span="10">
                <el-card shadow="hover" :body-style="{ height: '400px' }">
                    <div class="card-header">
                        <p class="card-header-title">国内攻击分布</p>
                        <p class="card-header-desc">最近一个月的订单来源统计</p>
                    </div>
                    <v-chart class="map-chart" :option="mapOptions" />
                </el-card>
            </el-col>
            <el-col :span="7">
                <el-card shadow="hover" :body-style="{ height: '400px' }">
                    <div class="card-header">
                        <p class="card-header-title">排行榜</p>
                        <p class="card-header-desc">销售商品的热门榜单Top5</p>
                    </div>
                    <div>
                        <div class="rank-item" v-for="(rank, index) in ranks">
                            <div class="rank-item-avatar">{{ index + 1 }}</div>
                            <div class="rank-item-content">
                                <div class="rank-item-top">
                                    <div class="rank-item-title">{{ rank.title }}</div>
                                    <div class="rank-item-desc">销量：{{ rank.value }}</div>
                                </div>
                                <el-progress :show-text="false" striped :stroke-width="10" :percentage="rank.percent"
                                    :color="rank.color" />
                            </div>
                        </div>
                    </div>
                </el-card>
            </el-col>
        </el-row> -->
    </div>
</template>

<script setup lang="ts" name="dashboard">
import countup from '@/components/countup.vue';
import { use, registerMap } from 'echarts/core';
import { BarChart, LineChart, PieChart, MapChart } from 'echarts/charts';
import { CirclePlusFilled, Search, Refresh } from '@element-plus/icons-vue';
import TableCustom from '@/components/table-custom-detect.vue';
import { ref, reactive } from 'vue';
import {
    GridComponent,
    TooltipComponent,
    LegendComponent,
    TitleComponent,
    VisualMapComponent,
} from 'echarts/components';
import { CanvasRenderer } from 'echarts/renderers';
import { fetchUserData } from '@/api';
import { User } from '@/types/user';
import VChart from 'vue-echarts';
import { dashOpt1, dashOpt2, mapOptions } from '../chart/options';
import chinaMap from '@/utils/china';
import { InfoFilled, StarFilled, WarnTriangleFilled } from '@element-plus/icons-vue';

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
const formInline = reactive({
    user: '',
    region: '',
    date: '',
    date1: '',
    date2: '',
    date3: '',
    date4: '',
    date5: '',
    date6: '',
    date7: '',
})

const tableData = ref<User[]>([]);

interface Detect {
    name: string
    desc: string
    rizhi:string
    moxing: string
    status:string
    starttime: string
    endtime: string
}

const detectData: Detect[] = [
    {
        name: '测试任务1',
        desc: '测试任务1',
        rizhi: '测试日志',
        moxing:"测试模型",
        status:"已完成",
        starttime: '2024-11-03 08:43:33',
        endtime: '2024-11-03 08:45:13',
    },
    {
        name: '测试任务2',
        desc: '测试任务2',
        rizhi: '测试日志',
        moxing:"测试模型",
        status:"已完成",
        starttime: '2024-11-05 16:29:13',
        endtime: '2024-11-05 16:31:29',
    },
    {
        name: '攻击检测任务1',
        desc: 'CADETS攻击检测',
        rizhi: 'ta1-cadets-e3',
        moxing:"KSAD攻击检测模型",
        status:"已完成",
        starttime: '2024-11-29 14:19:11',
        endtime: '2024-11-29 14:45:13',
    },
    
]
const page = reactive({
    index: 1,
    size: 10,
    total: 0,
})

const changePage = (val: number) => {
    page.index = val;
    getData();
};
const getData = async () => {
    const res = await fetchUserData()
    tableData.value = res.data.list;
    page.total = res.data.pageTotal;
};

use([
    CanvasRenderer,
    BarChart,
    GridComponent,
    LineChart,
    PieChart,
    TooltipComponent,
    LegendComponent,
    TitleComponent,
    VisualMapComponent,
    MapChart,
]);
registerMap('china', chinaMap);
const activities = [
    {
        content: '收藏商品',
        description: 'xxx收藏了你的商品，就是不买',
        timestamp: '30分钟前',
        color: '#00bcd4',
    },
    {
        content: '用户评价',
        description: 'xxx给了某某商品一个差评，吐血啊',
        timestamp: '55分钟前',
        color: '#1ABC9C',
    },
    {
        content: '订单提交',
        description: 'xxx提交了订单，快去收钱吧',
        timestamp: '1小时前',
        color: '#3f51b5',
    },
    {
        content: '退款申请',
        description: 'xxx申请了仅退款，又要亏钱了',
        timestamp: '15小时前',
        color: '#f44336',
    },
    {
        content: '商品上架',
        description: '运营专员瞒着你上架了一辆飞机',
        timestamp: '1天前',
        color: '#009688',
    },
];

let columns = ref([
    { prop: 'name', label: '任务名称', width: 200, align: 'center' },
    { prop: 'desc', label: '任务描述', width: 200, align: 'center' },
    // { prop: 'datatype', label: '数据类型' },
    { prop: 'rizhi', label: '系统溯源日志', width: 150, align: 'center' },
    { prop: 'moxing', label: '模型', width: 200, align: 'center' },
    { prop: 'starttime', label: '开始时间', width: 200, align: 'center' },
    { prop: 'endtime', label: '结束时间', width: 200, align: 'center' },
    { prop: 'status', label: '任务状态', width: 150, align: 'center' },
    { prop: 'operator', label: '操作' },
])

const ranks = [
    {
        title: '手机',
        value: 10000,
        percent: 80,
        color: '#f25e43',
    },
    {
        title: '电脑',
        value: 8000,
        percent: 70,
        color: '#00bcd4',
    },
    {
        title: '相机',
        value: 6000,
        percent: 60,
        color: '#64d572',
    },
    {
        title: '衣服',
        value: 5000,
        percent: 55,
        color: '#e9a745',
    },
    {
        title: '书籍',
        value: 4000,
        percent: 50,
        color: '#009688',
    },
];
const viewData = ref({
    row: {},
    list: []
});
// 查看详情弹窗相关
const visible1 = ref(false);

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
</script>

<style>
.card-body {
    display: flex;
    align-items: center;
    height: 100px;
    padding: 0;
}
</style>
<style scoped>
.card-content {
    flex: 1;
    text-align: center;
    font-size: 14px;
    color: #999;
    padding: 0 20px;
}

.card-num {
    font-size: 30px;
}

.card-icon {
    font-size: 50px;
    width: 100px;
    height: 100px;
    text-align: center;
    line-height: 100px;
    color: #fff;
}

.bg1 {
    background: #2d8cf0;
}

.bg2 {
    background: #64d572;
}

.bg3 {
    background: #e9a745;
}

.bg4 {
    background: #FF9999;
}

.color1 {
    color: #2d8cf0;
}

.color2 {
    color: #64d572;
}

.color3 {
    color: #e9a745;
}

.color4 {
    color: #FF9999;
}

.chart {
    width: 100%;
    height: 400px;
}

.card-header {
    padding-left: 10px;
    margin-bottom: 20px;
}

.card-header-title {
    font-size: 18px;
    font-weight: bold;
    margin-bottom: 5px;
}

.card-header-desc {
    font-size: 14px;
    color: #999;
}

.timeline-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    font-size: 16px;
    color: #000;
}

.timeline-time,
.timeline-desc {
    font-size: 12px;
    color: #787878;
}

.rank-item {
    display: flex;
    align-items: center;
    margin-bottom: 20px;
}

.rank-item-avatar {
    width: 40px;
    height: 40px;
    border-radius: 50%;
    background: #f2f2f2;
    text-align: center;
    line-height: 40px;
    margin-right: 10px;
}

.rank-item-content {
    flex: 1;
}

.rank-item-top {
    display: flex;
    justify-content: space-between;
    align-items: center;
    color: #343434;
    margin-bottom: 10px;
}

.rank-item-desc {
    font-size: 14px;
    color: #999;
}

.map-chart {
    width: 100%;
    height: 350px;
}

.flex-align {
    display: flex;
    align-items: center;
    gap: 8px;
    /* 图标与文字的间距 */
}

.icon {
    display: flex;
}
</style>