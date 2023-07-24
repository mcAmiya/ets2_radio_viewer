<script>
export default {
  data() {
    return {
      stations: [], // 存储表格数据的数组
      columns: [
        // 表格列定义
        {prop: 'url', label: 'URL'},
        {prop: 'name', label: '名称'},
        {prop: 'genre', label: '类型'},
        {prop: 'country', label: '国家'},
        {prop: 'bitrate', label: '比特率'},
        {prop: 'liked', label: '喜欢'},
      ],
    };
  },
  methods: {
    handleFileChange(event) {
      // 当用户选择本地文件后，触发change事件
      const file = event.target.files[0];
      if (file) {
        // 使用FileReader读取文件内容
        const reader = new FileReader();
        reader.onload = () => {
          try {
            const fileContent = reader.result;
            const parsedData = parseRadioData(fileContent); // 调用parseRadioData函数处理文件内容
            if (parsedData && Array.isArray(parsedData.stations)) {
              // 将解析处理后的stations数组直接赋值给表格的数据
              this.stations = parsedData.stations;
            }
          } catch (error) {
            console.error('解析文件内容失败：', error);
          }
        };
        reader.readAsText(file); // 以文本方式读取文件内容
      }
    },
    parseRadioData(content) {
      // 假设这里进行解析处理文件内容的逻辑
      // 返回包含stations数组的数据对象
      // 根据你提供的数据格式，应该是content.stations，如果不是，请根据实际情况进行处理
      return content.stations;
    },
  },
};
</script>


<template>
  <div class="table-container">
    <!-- input元素用于选择本地文件 -->
    <input type="file" @change="handleFileChange"/>
    <el-table :data="stations" border>
      <!-- 表格列定义 -->
      <el-table-column v-for="(item, index) in columns" :key="index" :prop="item.prop"
                       :label="item.label">
        <template v-if="item.prop === 'liked'" v-slot="{ row }">
          <a v-if="row.liked">⭐</a>
          <a v-else>⛤</a>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>


<style scoped>

</style>