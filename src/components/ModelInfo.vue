<template>
    <div class="f-container">
        <h2>Title</h2>
        <a-input-search size="large" v-model="searchQuery" @search="fetchData" placeholder="输入name"
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
const dataCfg = reactive({
    fields: {
        rows: ['name'],
        columns: ['score', 'score2', 'class1', 'class2', 'class3', 'class4'],
        values: [],
    },
    data: [],
});
const tableOptions = {
    width: 400,
    height: 400,
};

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
.content {
  display: flex;
  justify-content: space-around;
  padding: 40px;
  border-radius: 8px;
  /* box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1); */
  /* background-color: #ffffff; 白色背景 */
  /* background-color: #2a2a2a; */
  /* color: rgba(255, 255, 255, 0.87); */
  /* color: #213547; 深色字体 */
  min-width: 70vw;
  min-height: 60vh;
  font-size: 16px;
}

.table-container,
.chart-container {
  width: 45%;
  /* background-color: #2a2a2a; */
  padding: 10px;
  /* border-radius: 8px; */
  box-shadow: 0 1px 4px rgba(0, 0, 0, 0.1);
  overflow: auto;
}

.table-container,
.chart-container {
    background-color: #333; 
    color: rgba(255, 255, 255, 0.87); /* 浅色字体 */
    box-shadow: 0 1px 4px rgba(255, 255, 255, 0.1);
  }


.f-container {
    margin: 0 auto;
    padding: 4rem;
    text-align: center;
    font-size: 16px
}

body {
 
  background-image: url(data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSI0OCIgaGVpZ2h0PSI0OCIgZmlsbD0ibm9uZSI+PHBhdGggc3Ryb2tlPSIjMDAwIiBzdHJva2Utb3BhY2l0eT0iLjA0IiBzdHJva2Utd2lkdGg9Ii41IiBkPSJNLjI1LjI1aDQ3LjV2NDcuNUguMjV6Ii8+PC9zdmc+),linear-gradient(to bottom,#0000 40%,rgb(232 232 236));
  background-size: 48px 48px,100% 100%,100%;
}
</style>