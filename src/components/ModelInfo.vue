<template>
    <div class="f-container">
        <h2 style="margin-bottom: 1em;" >Title</h2>
        <a-input-search size="medium" v-model="searchQuery" @search="fetchData" placeholder="输入name"
            style="margin-bottom: 20px; width: 60%;" />
        <a-spin :spinning="loading">
            <div class="content">
                <div class="table-container">
                    <a-table :row-class-name="(_record, index) => (index % 2 === 1 ? 'table-striped' : null)"
                        :dataSource="dataSource" :columns="columns" bordered />
                </div>
                <div class="chart-container" ref="chartContainer"></div>
            </div>
        </a-spin>
    </div>
</template>

<script setup>
import { ref, reactive, onMounted, } from 'vue';
import axios from 'axios';
import { Chart } from '@antv/g2';

const searchQuery = ref('');
const loading = ref(false);


const columns = ref([
    { title: 'Score', dataIndex: 'score', key: 'score', sorter: (a, b) => a.score - b.score },
    { title: 'Score2', dataIndex: 'score2', key: 'score2' },
    { title: 'Model1', dataIndex: 'name1', key: 'name1' },
    { title: 'Model2', dataIndex: 'name2', key: 'name2' },
    { title: 'Model3', dataIndex: 'name3', key: 'name3' },
]);

const dataSource = ref([
    {
        "score": 2,
        "score2": 3,
        "name1": 1,
        "name2": 2,
        "name3": 3,
        "model": {
            "class1": 1,
            "class2": "a",
            "class3": "woman",
            "class4": "china"
        },
        "model2": {
            "class1": 2,
            "class2": "a",
            "class3": "woman",
            "class4": "china"
        },
        "model3": {
            "class1": 3,
            "class2": "a",
            "class3": "woman",
            "class4": "china"
        }
    }
]);

const chatData = ref([
    { name: 'model1', class: 'class1', 个数: 18 },
    { name: 'model1', class: 'class2', 个数: 28 },
    { name: 'model1', class: 'class3', 个数: 39 },

    { name: 'model2', class: 'class1', 个数: 12 },
    { name: 'model2', class: 'class2', 个数: 23 },
    { name: 'model2', class: 'class3', 个数: 34 },

    { name: 'model3', class: 'class1', 个数: 12 },
    { name: 'model3', class: 'class2', 个数: 23 },
    { name: 'model3', class: 'class3', 个数: 34 },
])

const chartContainer = ref(null);
let chart;

const fetchData = async () => {
    loading.value = true;

    try {
        const response = await axios.get('/mock.json', {
            params: { query: searchQuery.value },
        });
        
       
        const data = response.data;

        dataSource.value = data

        // 更新图表数据
        if (chart) {
            chart.changeData(chatData);
        } else {
            chart = new Chart({
                container: chartContainer.value,
                autoFit: true,
                height: 400,
            });

            chart
                .interval()
                .data(chatData)
                .encode('x', 'class')
                .encode('y', '个数')
                .encode('color', 'name')
                .transform({ type: 'dodgeX' })
                .interaction('elementHighlight', { background: true });

        }
    } catch (error) {
        console.error('Error fetching data:', error);
    } finally {
        loading.value = false;
    }
};

onMounted(() => {
    fetchData();
});
</script>

<style>
:root {
  font-family: Inter, system-ui, Avenir, Helvetica, Arial, sans-serif;
  line-height: 1.5;
  font-weight: 400;
  color-scheme: light dark;
  color: rgba(0, 0, 0, 0.87); /* 调整为暗色字体 */
  background-color: #f4f4f4; /* 浅色背景 */
  font-synthesis: none;
  text-rendering: optimizeLegibility;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.f-container {
  margin: 0 auto;
  padding: 2rem;
  text-align: center;
}

/* 内容容器样式 */
.content {
  display: flex;
  justify-content: center;
  align-items: stretch; /* 确保内容容器的高度一致 */
  gap: 20px;
  padding: 20px;
  border-radius: 8px;
  /* box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1); */
  /* background-color: #ffffff; 白色背景 */
  color: rgba(0, 0, 0, 0.87); /* 暗色字体 */
  max-width: 90vw; /* 增加最大宽度 */
  min-height: 70vh;
  margin: 0 auto; /* 保证内容居中 */
}

/* 表格和图表容器样式 */
.table-container,
.chart-container {
  width: 45%;
  background-color: #ffffff; /* 白色背景 */
  padding: 10px;
  border-radius: 8px;
  box-shadow: 0 1px 4px rgba(0, 0, 0, 0.1);
  color: rgba(0, 0, 0, 0.87); /* 暗色字体 */

}

/* 统一高度 */
.table-container {
  min-height: 300px; /* 设定最小高度以统一容器高度 */
}

.chart-container {
  min-height: 300px; /* 设定最小高度以统一容器高度 */
}


</style>