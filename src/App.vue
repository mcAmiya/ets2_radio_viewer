<template>
  <div class="common-layout">
    <el-container>
      <el-header>
        <el-row class="row-bg" justify="space-between">
          <!-- 你的按钮代码 -->
          <el-button @click="uploadFile" type="primary">

            <el-icon class="fa fa-edit icon"></el-icon>
            <span class="table-header-operate-text">导入</span>
          </el-button>

          <!--          <el-button @click="add" type="primary" class="table-header-operate">-->
          <!--            <el-icon class="fa fa-plus icon"></el-icon>-->
          <!--            <span class="table-header-operate-text">添加</span>-->
          <!--          </el-button>-->

          <!--          <el-button @click="edit" type="primary" :disabled="flase" class="table-header-operate">-->
          <!--            <el-icon class="fa fa-pencil icon"></el-icon>-->
          <!--            <span class="table-header-operate-text">编辑</span>-->
          <!--          </el-button>-->

<!--          <el-button @click="remove" type="danger" class="table-header-operate">-->
<!--            <el-icon class="fa fa-trash icon"></el-icon>-->
<!--            <span class="table-header-operate-text">删除</span>-->
<!--          </el-button>-->
        </el-row>
      </el-header>

      <el-main>
        <!-- input元素用于选择本地文件 -->
        <input
            type="file"
            ref="fileInput"
            style="display: none"
            @change="handleFileChange"/>

        <el-table
            :data="stations"
            border
            :header-cell-style="{'text-align':'center'}"
            :cell-style="{'text-align':'center'}"
        >
          <!-- 表格列定义 -->
          <el-table-column v-for="(item, index) in columns" :key="index" :prop="item.prop"
                           :label="item.label">
            <template v-if="item.prop === 'liked'" v-slot="{ row }">
              <a v-if="row.liked">⭐</a>
              <a v-else>⛤</a>
            </template>
          </el-table-column>

        </el-table>
      </el-main>
    </el-container>
  </div>
</template>


<script>

function decodeUnicode(str) {
  // 判断字符串中是否包含 %
  if (str.includes('%')) {
    return str; // 如果包含 %，则直接返回原始数据
  }

  // 将 \\x 替换成 %
  const encodedStr = str.replace(/\\x/g, '%');
  // 使用 decodeURIComponent 进行解码
  return decodeURIComponent(encodedStr);
}

function parseRadioData(data) {
  const regexNameless = /live_stream_def\s*:\s*([^{\n]+)/;
  const namelessMatch = regexNameless.exec(data);
  const namelessField = namelessMatch ? namelessMatch[1].trim() : '';

  const regex = /stream_data\[\d+\]:\s*"([^"]+)"/g;
  const stations = [];
  let match;

  while ((match = regex.exec(data)) !== null) {
    const stationData = match[1];
    const station = {
      url: decodeUnicode(stationData.split("|")[0]), // 对 URL 进行转义
      name: decodeUnicode(stationData.split("|")[1]), // 对电台名称进行转义
      genre: decodeUnicode(stationData.split("|")[2]), // 对类型进行转义
      country: decodeUnicode(stationData.split("|")[3]), // 对国家进行转义
      bitrate: parseInt(stationData.split("|")[4]),
      liked: parseInt(stationData.split("|")[5]) === 1,
    };
    stations.push(station);
  }

  return { namelessField, stations };
}

export default {
  data() {
    return {
      // 导入文件
      selectedFile: null,
      // 表格
      stations: [], // 存储表格数据的数组
      columns: [
        // 表格列定义
        {prop: 'liked', label: '⭐'},
        {prop: 'name', label: '名称'},
        {prop: 'genre', label: '流派'},
        {prop: 'country', label: '语言'},
        {prop: 'bitrate', label: '比特率'},
        {prop: 'url', label: 'URL'},
      ],
    };
  },
  methods: {
    remove() {

      const data = XLSX.utils.json_to_sheet(this.columns)//此处tableData.value为表格的数据
      const wb = XLSX.utils.book_new()
      XLSX.utils.book_append_sheet(wb, data, 'test-data')//test-data为自定义的sheet表名
      XLSX.writeFile(wb, 'test.xlsx')//test.xlsx为自定义的文件名
    },
    uploadFile() {
      // 触发点击文件输入框的操作
      this.$refs.fileInput.click();
    },
    handleFileInputChange(event) {
      const file = event.target.files[0];
      if (file) {
        // 处理你的文件上传逻辑
        // 例如，可以将文件存储在data中，并更新selectedFile的值来显示文件名
        this.selectedFile = file;
      }
    },
    add() {
      console.log(1)
    },
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
  }
  ,
}
;
</script>
