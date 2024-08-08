<template>
    <div class="f-container">
        <h2 style="margin-bottom: 1em;" >Title</h2>
        <a-input-search size="medium" v-model="searchQuery" @search="fetchData" placeholder="输入name"
            style="margin-bottom: 20px; width: 60%;" />
        <a-spin :spinning="loading">
            <div class="content" :class="{'h-content': dataSource.length <= 0}">
                <div class="table-container">
                    <a-table :row-class-name="(_record, index) => (index % 2 === 1 ? 'table-striped' : null)"
                        :dataSource="dataSource" :columns="columns" bordered>
                        <template #bodyCell="{ column, record }">
                            <a-tooltip v-if="shouldShowTooltip(column.key)">
                                <template #title>
                                    <span v-if="column.dataIndex === 'name1'" >{{ record['model'] }}</span>
                                    <span v-else-if="column.dataIndex === 'name2'">{{ record['model2'] }}</span>
                                    <span v-else-if="column.dataIndex === 'name3'">{{ record['model3'] }}</span>
                                </template>
                                <span>{{ record[column.dataIndex] }}</span>
                            </a-tooltip>
                            <span v-else>{{ record[column.dataIndex] }}</span>
                        </template>
                    </a-table>
                </div>
                <div class="chart-container" ref="chartContainer"></div>
            </div>
            <div class="empty-container" v-if="!dataSource.length">
                <a-empty description="暂无数据，请搜索查询。" />
            </div>
        </a-spin>
    </div>
</template>

<script setup>
import { ref, reactive, onMounted, h } from 'vue';
import axios from 'axios';
import { Chart } from '@antv/g2';

const searchQuery = ref('');
const loading = ref(false);


const columns = ref([
    { title: 'Score', dataIndex: 'score', key: 'score', sorter: (a, b) => a.score - b.score },
    { title: 'Score2', dataIndex: 'score2', key: 'score2' },
    { title: 'Model1', dataIndex: 'name1', key: 'name1', },
    { title: 'Model2', dataIndex: 'name2', key: 'name2' },
    { title: 'Model3', dataIndex: 'name3', key: 'name3' },
]);
const tooltipColumns = ['name1', 'name2', 'name3']; // 需要添加 Tooltip 的列

const shouldShowTooltip = (columnKey) => {
    return tooltipColumns.includes(columnKey);
};

const getTooltipContent = (record) => {
      // 获取与当前列相关的 model 对象
    let model;
    if (record.model && record.name1 !== undefined) model = record.model;
    else if (record.model2 && record.name2 !== undefined) model = record.model2;
    else if (record.model3 && record.name3 !== undefined) model = record.model3;

    if (model) {
        return h('ul', [
            h('li', `Class1: ${model.class1}`),
            h('li', `Class2: ${model.class2}`),
            h('li', `Class3: ${model.class3}`),
            h('li', `Class4: ${model.class4}`)
        ]);
    }
    return '';
};
const dataSource = ref([]);

const chatData = ref([])

const chartContainer = ref(null);

let chart;

const updateChart = (data = []) => {
    if (chart) {
        chart.changeData(data);
    } else {
        chart = new Chart({
            container: chartContainer.value,
            autoFit: true,
            height: 400,
        });

        chart
            .interval()
            .data(data)
            .encode('x', 'class')
            .encode('y', '个数')
            .encode('color', 'name')
            .transform({ type: 'dodgeX' })
            .interaction('elementHighlight', { background: true });

    }
}

const fetchData = async () => {
    loading.value = true;

    try {
        const response = await axios.get('/mock.json', {
            params: { query: searchQuery.value },
        });
        
       
        const data = response.data;

        dataSource.value = data
        
        chatData.value = [
            { name: 'model1', class: 'class1', 个数: 18 },
            { name: 'model1', class: 'class2', 个数: 28 },
            { name: 'model1', class: 'class3', 个数: 39 },

            { name: 'model2', class: 'class1', 个数: 12 },
            { name: 'model2', class: 'class2', 个数: 23 },
            { name: 'model2', class: 'class3', 个数: 34 },

            { name: 'model3', class: 'class1', 个数: 12 },
            { name: 'model3', class: 'class2', 个数: 23 },
            { name: 'model3', class: 'class3', 个数: 34 },
        ]

        updateChart(chatData.value);

    } catch (error) {
        console.error('Error fetching data:', error);
    } finally {
        loading.value = false;
    }
};



onMounted(() => {
    updateChart();
});
</script>

<style>
:root {
  font-family: Inter, system-ui, Avenir, Helvetica, Arial, sans-serif;
  line-height: 1.5;
  font-weight: 400;
  color-scheme: light dark;
  color: rgba(0, 0, 0, 0.87); 
  background-color: #f4f4f4; 
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


.content {
  display: flex;
  justify-content: center;
  align-items: stretch; 
  gap: 20px;
  padding: 20px;
  border-radius: 8px;
  
  color: rgba(0, 0, 0, 0.87); 
  max-width: 90vw; 
  min-height: 70vh;
  margin: 0 auto;
}


.table-container,
.chart-container {
  width: 45%;
  background-color: #ffffff; 
  padding: 10px;
  border-radius: 8px;
  box-shadow: 0 1px 4px rgba(0, 0, 0, 0.1);
  color: rgba(0, 0, 0, 0.87); 
  min-height: 300px; 
  overflow: auto;
}

.h-content {
    opacity: 0;
    position: relative;
}

.empty-container {
    position: absolute;
    top: 30%;
    left: 50%;
    transform: translate(-50%, -50%);

}


body {
    background-image: url(data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSI0OCIgaGVpZ2h0PSI0OCIgZmlsbD0ibm9uZSI+PHBhdGggc3Ryb2tlPSIjMDAwIiBzdHJva2Utb3BhY2l0eT0iLjA0IiBzdHJva2Utd2lkdGg9Ii41IiBkPSJNLjI1LjI1aDQ3LjV2NDcuNUguMjV6Ii8+PC9zdmc+),linear-gradient(to bottom,#0000 40%,rgb(232 232 236));
    background-size: 48px 48px,100% 100%,100%;
}


</style>