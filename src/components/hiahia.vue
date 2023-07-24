<script>
export default {
  data() {
    return {
      // ...other data...
    };
  },
  methods: {
    // ...other methods...

    // Double-click editing functionality
    handleRowDblclick(row) {
      row.editable = true;
    },

    saveEditedData(row) {
      row.editable = false;
      // Save the edited data (You can implement the saving logic here)
    },

    cancelEdit(row) {
      row.editable = false;
      // If needed, you can revert the data back to its original value here
    },
  },
};
</script>


<template>
<el-table-column label="名称" prop="name">
  <template v-slot="{ row }">
    <span v-if="!row.editable" @dblclick="handleRowDblclick(row)">{{ row.name }}</span>
    <el-input v-else v-model="row.name" @blur="saveEditedData(row)" @keyup.enter="saveEditedData(row)"></el-input>
  </template>
</el-table-column>

</template>

<style scoped>

</style>