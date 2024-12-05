<template>
	<div class="search-container">
		<el-form ref="searchRef" :model="query" :inline="true">
			<el-form-item>
				<el-button type="primary" @click="dialogFormVisible = true" style="margin-right: 10px;"><el-icon
						class="el-icon--right">
						<UploadFilled />
					</el-icon>上传文件</el-button>
				<el-button type="primary" style="margin-right: 10px;"><el-icon class="el-icon--right">
						<DeleteFilled />
					</el-icon>删除文件</el-button>
				<el-button type="primary" style="margin-right: 10px;"><el-icon class="el-icon--right">
						<Refresh />
					</el-icon>刷新</el-button>

			</el-form-item>
			<!-- 文本框、下拉框、日期框 -->
			<!-- <el-form-item :label="item.label" :prop="item.prop" v-for="item in options">	
				<el-input v-if="item.type === 'input'" v-model="query[item.prop]" :disabled="item.disabled"
					:placeholder="item.placeholder" clearable></el-input>
				<el-select v-else-if="item.type === 'select'" v-model="query[item.prop]" :disabled="item.disabled"
					:placeholder="item.placeholder" clearable>
					<el-option v-for="opt in item.opts" :label="opt.label" :value="opt.value"></el-option>
				</el-select>
				<el-date-picker v-else-if="item.type === 'date'" type="date" v-model="query[item.prop]"
					:value-format="item.format"></el-date-picker>
			</el-form-item>
			<el-form-item>
				<el-button type="primary" :icon="Search" @click="search">搜索</el-button>
				<el-button :icon="Refresh" @click="resetForm(searchRef)">重置</el-button>
			</el-form-item> -->
		</el-form>
	</div>

	<el-dialog v-model="dialogFormVisible" title="上传文件" width="600">
		<el-form :model="form">

			<el-form-item label="选择文件类型" :label-width="formLabelWidth">
				<el-radio-group v-model="radio1" size="large"
					style="width: 100%; display: flex; justify-content: center; gap: 20px;">
					<el-radio-button label="网络威胁情报" value="网络威胁情报" />
					<el-radio-button label="系统审计日志" value="系统审计日志" />
				</el-radio-group>
				<!-- <el-input v-model="form.name" autocomplete="off" /> -->
			</el-form-item>
			<el-form-item label="选择文件" :label-width="formLabelWidth">
				<el-upload class="upload-demo" drag
					action="https://run.mocky.io/v3/9d059bf9-4660-45f2-925d-ce80ad6c4d15" multiple style="width: 100%;">
					<el-icon class="el-icon--upload"><upload-filled /></el-icon>
					<div class="el-upload__text" style="line-height: 1.5; font-size: 14px;">
						<el-text tag="b">点击此处或拖拽文件上传</el-text>
						<br>
						<el-text class="mx-1">支持单个或批量上传</el-text>

					</div>
				</el-upload>

				<!-- <el-button type="primary" plain>上传文件</el-button> -->
				<!-- <el-select v-model="form.region" placeholder="Please select a zone">
          <el-option label="Zone No.1" value="shanghai" />
          <el-option label="Zone No.2" value="beijing" />
        </el-select> -->
			</el-form-item>
			<el-form-item label="文件描述" :label-width="formLabelWidth">
				<el-input v-model="textarea" style="width: 100%" :rows="2" type="textarea" placeholder="" />
			</el-form-item>
		</el-form>
		<template #footer>
			<div class="dialog-footer">
				<el-button @click="dialogFormVisible = false">取消</el-button>
				<el-button type="primary" @click="dialogFormVisible = false">
					确认上传
				</el-button>
			</div>
		</template>
	</el-dialog>
</template>

<script lang="ts" setup>
import { FormInstance } from 'element-plus';
import { Search, Refresh } from '@element-plus/icons-vue';
import { PropType, ref, reactive } from 'vue';
import { FormOptionList } from '@/types/form-option';

const radio1 = ref('网络威胁情报')
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


const props = defineProps({
	query: {
		type: Object,
		required: true
	},
	options: {
		type: Array as PropType<Array<FormOptionList>>,
		required: true
	},
	search: {
		type: Function,
		default: () => { }
	}
});

const textarea = ref('')
const searchRef = ref<FormInstance>();
const resetForm = (formEl: FormInstance | undefined) => {
	if (!formEl) return
	formEl.resetFields()
	props.search();
}
</script>

<style scoped>
.search-container {
	padding: 20px 30px 0;
	background-color: #fff;
	margin-bottom: 10px;
	border: 1px solid #ddd;
	border-radius: 5px
}
</style>
