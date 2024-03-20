<template>
  <div>
    <div style="text-align: center">
      <el-table
        :data="
          tableData.filter(
            (data) =>
              !search || data.name.toLowerCase().includes(search.toLowerCase())
          )
        "
        style="width: 100%"
      >
        <!-- <el-table-column label="Date" prop="date"> </el-table-column> -->
        <el-table-column label="Name" prop="name"> </el-table-column>
        <el-table-column align="right">
          <template slot="header" slot-scope="scope">
            <el-input v-model="search" size="mini" placeholder="Search" />
          </template>
          <template slot-scope="scope">
            <el-button size="mini" @click="handleEdit(scope.$index, scope.row)"
              >Edit</el-button
            >
            <el-button
              size="mini"
              type="danger"
              @click="handleDelete(scope.$index, scope.row)"
              >Delete</el-button
            >
          </template>
        </el-table-column>
      </el-table>
      <el-button @click="handleAdd()" round style="margin-top: 20px"
        >Add Device</el-button
      >
    </div>

    <el-dialog
      title="Edit"
      :visible.sync="dialogVisible"
      width="400px"
      :before-close="handleClose"
    >
      <div style="display: flex">
        <div class="title-frame">Name:</div>
        <div>
          <el-input v-model="selected.name" :disabled="true"></el-input>
        </div>
      </div>

      <div style="display: flex; margin-top: 20px">
        <div class="title-frame">CPU frequency:</div>
        <div>
          <el-input v-model="selected.cpuFrequency"></el-input>
        </div>
      </div>

      <div style="display: flex; margin-top: 20px">
        <div class="title-frame">SRAM:</div>
        <div>
          <el-input v-model="selected.sram"></el-input>
        </div>
      </div>

      <div style="display: flex; margin-top: 20px">
        <div class="title-frame">Flash:</div>
        <div>
          <el-input v-model="selected.flash"></el-input>
        </div>
      </div>

      <div style="display: flex; margin-top: 20px">
        <div class="title-frame">CPU Architecture:</div>
        <div>
          <el-input v-model="selected.cpuArch"></el-input>
        </div>
      </div>

      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">Cancel</el-button>
        <el-button type="primary" @click="modifyDeviceInfo">Confirm</el-button>
      </span>
    </el-dialog>
    <el-dialog
      title="Add Device"
      :visible.sync="addDialogVisible"
      width="400px"
      :before-close="handleClose"
    >
      <div style="display: flex">
        <div class="title-frame">Name:</div>
        <div>
          <el-input v-model="addDeviceInfo.name"></el-input>
        </div>
      </div>

      <div style="display: flex; margin-top: 20px">
        <div class="title-frame">CPU frequency:</div>
        <div>
          <el-input v-model="addDeviceInfo.cpuFrequency"></el-input>
        </div>
      </div>

      <div style="display: flex; margin-top: 20px">
        <div class="title-frame">SRAM:</div>
        <div>
          <el-input v-model="addDeviceInfo.sram"></el-input>
        </div>
      </div>

      <div style="display: flex; margin-top: 20px">
        <div class="title-frame">Flash:</div>
        <div>
          <el-input v-model="addDeviceInfo.flash"></el-input>
        </div>
      </div>

      <div style="display: flex; margin-top: 20px">
        <div class="title-frame">CPU Architecture:</div>
        <div>
          <el-input v-model="addDeviceInfo.cpuArch"></el-input>
        </div>
      </div>

      <span slot="footer" class="dialog-footer">
        <el-button @click="addDialogVisible = false">Cancel</el-button>
        <el-button type="primary" @click="addDevice">Confirm</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tableData: [],
      search: "",
      selected: {},
      addDeviceInfo: {},
      dialogVisible: false,
      addDialogVisible: false,
    };
  },
  methods: {
    handleEdit(index, row) {
      // console.log(index, row);
      this.selected = row;
      // this.selected.name = row.name;
      // this.selected.cpuFrequency = row.cpuFrequency;
      // this.selected.sram = row.sram;
      // this.selected.flash = row.flash;
      // this.selected.cpuArch = row.cpuArch;

      this.dialogVisible = true;
      console.log(row);
    },
    handleAdd() {
      this.addDialogVisible = true;
    },
    handleDelete(index, row) {
      console.log(index, row);
    },
    handleClose(done) {
      // this.$confirm("确认关闭？")
      //   .then((_) => {
      //     done();
      //   })
      //   .catch((_) => {});
      this.dialogVisible = false;
      this.addDialogVisible = false;
    },
    addHandleClose(done) {
      this.addDialogVisible = false;
    },
    findAllDevice() {
      this.$http.get("http://localhost:8081/findAllDevice").then((res) => {
        this.tableData = res.data;
        console.log(this.tableData);
      });
    },
    modifyDeviceInfo() {
      const formData = new FormData();
      formData.append("id", this.selected.id);
      formData.append("name", this.selected.name);
      formData.append("cpuFrequency", this.selected.cpuFrequency);
      formData.append("sram", this.selected.sram);
      formData.append("flash", this.selected.flash);
      formData.append("cpuArch", this.selected.cpuArch);
      this.$http
        .post("http://localhost:8081/modifyDeviceInfo", formData)
        .then((res) => {
          console.log(res);
        });
      this.dialogVisible = false;
    },
    addDevice() {
      const formData = new FormData();
      formData.append("name", this.addDeviceInfo.name);
      formData.append("cpuFrequency", this.addDeviceInfo.cpuFrequency);
      formData.append("sram", this.addDeviceInfo.sram);
      formData.append("flash", this.addDeviceInfo.flash);
      formData.append("cpuArch", this.addDeviceInfo.cpuArch);
      console.log(this.addDeviceInfo.name);
      this.$http
        .post("http://localhost:8081/addDevice", formData)
        .then((res) => {
          console.log(res);
        });
      this.addDialogVisible = false;
    },
  },
  mounted() {
    this.findAllDevice();
  },
};
</script>

<style>
.title-frame {
  font-weight: bold;
  margin-top: 10px;
  margin-right: 10px;
  width: 80px;
}
</style>
