<template>
  <div class="outer-frame">
    <div class="frame">
      <div class="device-frame">
        <div>
          <h2>Please select your device</h2>
        </div>
        <el-select
          v-model="deviceValue"
          filterable
          placeholder="Select"
          no-data-text="no data"
          @change="getDeviceInfo"
        >
          <el-option
            v-for="item in devices"
            :key="item.id"
            :label="item.name"
            :value="item.id"
          >
          </el-option>
        </el-select>
      </div>

      <div class="model-frame">
        <div>
          <h2>Please select your model</h2>
        </div>
        <el-select
          v-model="modelValue"
          filterable
          placeholder="Select"
          no-data-text="no data"
          @change="getModelInfo"
        >
          <el-option
            v-for="item in models"
            :key="item.id"
            :label="item.name"
            :value="item.id"
          >
          </el-option>
        </el-select>
      </div>

      <div class="button-frame">
        <el-button type="primary" plain>Deploy</el-button>
      </div>
    </div>

    <div class="info-frame">
      <div style="margin-top: 60px">
        <h2>Device Information</h2>
        <div style="display: flex">
          <div style="font-weight: bold">Name:</div>
          {{ this.deviceInfo.name }}
        </div>
        <div style="display: flex">
          <div style="font-weight: bold">CPU Frequency:</div>
          {{ this.deviceInfo.cpuFrequency }}(MHZ)
        </div>
        <div style="display: flex">
          <div style="font-weight: bold">SRAM:</div>
          {{ this.deviceInfo.sram }}(KB)
        </div>
        <div style="display: flex">
          <div style="font-weight: bold">flash:</div>
          {{ this.deviceInfo.flash }}(KB)
        </div>
        <div style="display: flex">
          <div style="font-weight: bold">CPU architecture:</div>
          {{ this.deviceInfo.cpuArch }}
        </div>
      </div>

      <div style="margin-top: 60px">
        <h2>Model Information</h2>
        <div style="display: flex">
          <div style="font-weight: bold">Name:</div>
          {{ this.modelInfo.name }}
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      devices: [],
      models: [],
      deviceInfo: {
        id: 0,
        name: "",
        cpuFrequency: 0,
        sram: 0,
        flash: 0,
        cpuArch: "",
      },
      modelInfo: {
        id: 0,
        name: "",
      },
      deviceValue: "",
      modelValue: "",
    };
  },
  methods: {
    getDeviceInfo() {
      console.log(this.deviceValue);
      const formData = new FormData();
      formData.append("id", this.deviceValue);
      this.$http
        .post("http://localhost:8081/findDeviceInfo", formData)
        .then((res) => {
          console.log(res);
          this.deviceInfo.name = res.data.name;
          this.deviceInfo.cpuFrequency = res.data.cpuFrequency;
          this.deviceInfo.sram = res.data.sram;
          this.deviceInfo.flash = res.data.flash;
          this.deviceInfo.cpuArch = res.data.cpuArch;
        });
    },
    getModelInfo() {
      console.log(this.modelValue);
      this.modelInfo.name = this.models[this.modelValue - 1].name;
    },
    findAllDevices() {
      this.$http.get("http://localhost:8081/findAllDevice").then((res) => {
        var data = res.data;
        this.devices = data;
      });
    },
    findAllDeviceAndModel() {
      this.$http
        .get("http://localhost:8081/findAllDeviceAndModel")
        .then((res) => {
          var data = res.data;
          this.devices = data.deviceDtoList;
          this.models = data.modelDtoList;
          console.log(data);
        });
    },
    fetchConnectedDevice() {
      this.$http
        .get("http://localhost:8081/fetchConnectedDevice")
        .then((res) => {
          console.log(res);
        });
    },
  },
  mounted() {
    // this.findAllDevices();
    this.findAllDeviceAndModel();
    console.log(this.devices);
  },
};
</script>

<style scoped>
.outer-frame {
  display: flex;
  width: 800px;
  margin: 0px auto;
}
.frame {
  text-align: left;
  width: 400px;
  /* margin: 0px auto; */
}
.info-frame {
  text-align: left;
  width: 400px;
  /* margin: 0px auto; */
}
.device-frame {
  margin-top: 60px;
}
.model-frame {
  margin-top: 60px;
}
.button-frame {
  margin-top: 60px;
}
</style>
