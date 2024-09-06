<template>
  <v-layout class="rounded-md">
    <v-app-bar color="primary" title="Application Bar"></v-app-bar>

    <!-- Navigation Drawer with Buttons -->
    <v-navigation-drawer app>
      <v-list>
        <v-list-item title="แผงควบคุมโรงเรือนที่ 1"></v-list-item>
        <v-divider></v-divider><br>

        <!-- Control Buttons inside Drawer -->
        <v-card class="mb-1">
          <v-list-item>
            <v-list-item-content>
              <v-btn block color="green" class="mb-2" @click="start('start')">START</v-btn>
              <v-btn block color="red" class="mb-2" @click="stop('stop')">STOP</v-btn>
            </v-list-item-content>
          </v-list-item>
        </v-card>
        <v-row class="d-flex align-center justify-center">
          <v-col cols="6">
            <br><br>
            <div class="rounded-circle mx-auto"
              :style="{ height: '20px', width: '20px', background: isOn ? 'green' : '#EEE' }">
            </div>
            ON
          </v-col>
          <v-col cols="6">
            <br><br>
            <div class="rounded-circle mx-auto"
              :style="{ height: '20px', width: '20px', background: !isOn ? 'red' : '#EEE' }">
            </div>
              OFF
          </v-col>
        </v-row>
      </v-list>
    </v-navigation-drawer>

    <v-main class="d-flex align-center justify-center " style="min-height: 600px;">
      <div class="text-center">
        <div class="rectangular-container">
          <div class="text-content">
            <h3>อุณหภูมิ</h3>
          </div>
          <v-progress-circular :model-value="value_temp" :rotate="360" :size="100" :width="15" color="green">
            <template v-slot:default> {{ value_temp }} °C </template>
          </v-progress-circular>
        </div>
        <div class="rectangular-container">
          <div class="text-content">
            <h3>ความชื้น</h3>
          </div>
          <v-progress-circular :model-value="value_humi" :rotate="360" :size="100" :width="15" color="red">
            <template v-slot:default> {{ value_humi }} % </template>
          </v-progress-circular>
        </div>
      </div><br>
      <div class="text-center">
        <div class="rectangular-container">
          <div class="text-content">
            <h3>อุณหภูมิที่เหมาะสม</h3>
            <p>ต่ำสุด: {{ value_t1 }} °C</p>
            <p>ไม่เกิน: {{ value_t2 }} °C</p>

          </div>
          <v-progress-circular :model-value="totalValueT" :rotate="360" :size="100" :width="15" color="green">
            <template v-slot:default> {{ totalValueT }} °C </template>
          </v-progress-circular>
        </div>
        <div class="rectangular-container">
          <div class="text-content">
            <h3>ความชื้นที่เหมาะสม</h3>
            <p>ต่ำสุด: {{ value_h1 }} °C</p>
            <p>ไม่เกิน: {{ value_h2 }} °C</p>
          </div>
          <v-progress-circular :model-value="totalValueH" :rotate="360" :size="100" :width="15" color="red">
            <template v-slot:default> {{ totalValueH }} % </template>
          </v-progress-circular>
        </div>
      </div>
    </v-main>

    <!-- Right Drawer (if needed) -->
    <v-navigation-drawer location="right">
      <v-list>
        <v-list-item title="ไฟสถานะต่างๆ"></v-list-item>
        <v-divider></v-divider><br>
        <v-card>
          <div style="text-align: center;">
            <v-list-item title="ไฟสถานะการทำงาน"></v-list-item>
          </div>
          <div style="  display: flex; flex-wrap: wrap; justify-content: center;align-items: center; font-size: 50px; ">
            <v-icon :color="isRunning ? 'green' : 'red'">
              {{ isRunning ? 'mdi-power' : 'mdi-power-off' }}
            </v-icon>
          </div>
        </v-card><br>
        <v-card>
          <div style="text-align: center;">
            <v-list-item title="มอเตอร์ละบายความชื่น"></v-list-item>
          </div>
          <div style="  display: flex; flex-wrap: wrap; justify-content: center;align-items: center; font-size: 50px; ">
            <v-icon :color="isTeamp ? 'green' : 'red'">
              {{ isTeamp ? 'mdi-cloud-percent' : 'mdi-cloud-percent-outline' }}
            </v-icon>
          </div>
        </v-card><br>
        <v-card>
          <div style="text-align: center;">
            <v-list-item title="มอเตอร์ละบายความร้อน"></v-list-item>
          </div>
          <div style="  display: flex; flex-wrap: wrap; justify-content: center;align-items: center; font-size: 50px; ">
            <v-icon :color="isHumi ? 'green' : 'red'">
              {{ isHumi ? 'mdi-heat-pump' : 'mdi-heat-pump-outline' }}
            </v-icon>
          </div>
        </v-card><br>

      </v-list>
    </v-navigation-drawer>
  </v-layout>
</template>

<script>
import mqtt from "mqtt";

definePageMeta({
  layout: "defa",
});

export default {
    data() {
        return {
            value: 0,
            value1: 0,
            originalValue: 0,
            originalValue1: 0,
            isOn: false,
            value_temp: 0,
            value_humi: 0,
            value_state: "",
            value_t1: 0,
            value_t2: 0,
            value_h1: 0,
            value_h2: 0,
            isOnlight: false,
            lightStates: [false, false, false], // Array to hold the state of each light
            stepIndex: 0, // To track the current step
            statustem: "",
            systemStatus: "",
            systemStatus1: "",
        };
    },

    computed: {
        totalValue() {
            const ttotal = Number(this.value_temp) || 0;
            const htotal = Number(this.value_humi) || 0;
            return ttotal + htotal;
        },
        // New computed properties for the averages
        // คำนวณค่าเฉลี่ยอุณหภูมิ
        totalValueT() {
            const t1 = Number(this.value_t1) || 0;
            const t2 = Number(this.value_t2) || 0;
            return (t1 + t2) / 2;
        },
        // คำนวณค่าเฉลี่ยความชื้น
        totalValueH() {
            const h1 = Number(this.value_h1) || 0;
            const h2 = Number(this.value_h2) || 0;
            return (h1 + h2) / 2;
        },
        stepButtonLabel() {
            return `Step ${this.stepIndex + 1}`;
        },
        temperatureStatus() {
            if (this.value_temp == (this.value_t1 + this.value_t2) / 2) return 'appropriate';
            if (this.value_temp >= this.value_t1 && this.value_temp <= this.value_t2 && this.value_temp > (this.value_t1 + this.value_t2) / 2) return 'quite hot';
            if (this.value_temp >= this.value_t1 && this.value_temp <= this.value_t2 && this.value_temp < (this.value_t1 + this.value_t2) / 2) return 'quite cold';
            if (this.value_temp > this.value_t2) return 'hot';
            if (this.value_temp < this.value_t1) return 'cold';

            return 'inappropriate';
        },
        humidityStatus() {
            if (this.value_humi == (this.value_h1 + this.value_h2) / 2) return 'appropriate';
            if (this.value_humi >= this.value_h1 && this.value_humi <= this.value_h2 && this.value_humi < (this.value_h1 + this.value_h2) / 2) return 'quite dry';
            if (this.value_humi >= this.value_h1 && this.value_humi <= this.value_h2 && this.value_humi > (this.value_h1 + this.value_h2) / 2) return 'quite damp';
            if (this.value_humi < this.value_h1) return 'dry';
            if (this.value_humi > this.value_h2) return 'damp';
            return 'inappropriate';
        },
    },

    created() {
        this.client = mqtt.connect('ws://broker.emqx.io:8083/mqtt');
        this.client.on('connect', this.onMqttConnect.bind(this));
        this.client.on('message', this.onMqttMessage.bind(this));
    },

    beforeDestroy() {
        this.client && this.client.end();
    },

    methods: {
        start() {
            this.isOn = true;
            this.value_temp = this.originalValue;
            this.value_humi = this.originalValue1;
            this.stepIndex = 0; // Reset step index when starting
        },
        stop() {
            this.isOn = false;
            this.value_temp = 0;
            this.value_humi = 0;
            this.resetValues(); // Reset values when stopping
            this.lightStates = [false, false, false]; // Turn off all lights
            this.value_t1 = 0;
            this.value_t2 = 0;
            this.value_h1 = 0;
            this.value_h2 = 0;
            this.value_state = "ถูกปิดการใช้งาน";
            this.systemStatus = "ระบบกำลังรอข้อมูลเข้ามา.";
            this.systemStatus1 = "ระบบกำลังรอข้อมูลเข้ามา";
        },
        resetValues() {
            this.value_temp = 0;
            this.value_humi = 0;
            this.lightStates = [false, false, false];
            this.value_t1 = 0;
            this.value_t2 = 0;
            this.value_h1 = 0;
            this.value_h2 = 0;
            this.value_state = "ถูกปิดการใช้งาน";
            this.systemStatus = "ระบบกำลังรอข้อมูลเข้ามา.";
            this.systemStatus1 = "ระบบกำลังรอข้อมูลเข้ามา";
        },
        onMqttConnect() {
            this.client.publish('op', 'status');
            this.client.subscribe('status');
            this.client.subscribe('temperature');
            this.client.subscribe('moisture');
        },
        onMqttMessage(topic, message) {
            if (topic === 'status') {
                this.msg = message.toString();
            }
            if (topic === 'temperature' && this.isOn) {
                let data1 = JSON.parse(message.toString());
                this.value_temp = parseFloat(data1.temp);
                this.value_humi = parseFloat(data1.humi);
                this.value_state = data1.state;
                this.value_t1 = parseFloat(data1.t1);
                this.value_t2 = parseFloat(data1.t2);
                this.value_h1 = parseFloat(data1.h1);
                this.value_h2 = parseFloat(data1.h2);

                // console.log("Temperature:", this.value_temp);
                // console.log("Humidity:", this.value_humi);
                // console.log("Temp min:", this.value_t1);
                // console.log("Temp max:", this.value_t2);
                // console.log("Humidity min:", this.value_h1);
                // console.log("Humidity max:", this.value_h2);

                this.updateLightStates(); // อัปเดตสถานะไฟหลังจากได้รับข้อมูลอุณหภูมิ
            }
        },
        updateLightStates() {
            const tempStatus = this.temperatureStatus;
            const humiStatus = this.humidityStatus;
            console.log(`tempStatus: ${tempStatus}, humiStatus: ${humiStatus}`);

            // กรณีที่ทั้งอุณหภูมิและความชื้นเหมาะสม
            if (tempStatus === 'appropriate' && humiStatus === 'appropriate') {
                this.lightStates = [false, true, false]; // Green only
                this.systemStatus = "เหมาะสม";
                this.systemStatus1 = "เหมาะสม";
            }
            // กรณีที่อุณหภูมิสูงเกินไปหรือความชื้นสูงเกินไป
            else if (tempStatus === 'hot' && humiStatus === 'damp') {
                this.lightStates = [true, true, true]; // Red only
                this.systemStatus = "ไม่เหมาะสม";
                this.systemStatus1 = "ไม่เหมาะสม";

            } // กรณีที่อุณหภูมิต่ำเกินไปหรือความชื้นต่ำเกินไป
            else if (tempStatus === 'cold' && humiStatus === 'dry') {
                this.lightStates = [true, true, true]; // Blue only
                this.systemStatus = "ไม่เหมาะสม";
                this.systemStatus1 = "ไม่เหมาะสม";
            }
            else if (tempStatus === 'hot' || humiStatus === 'dry') {
                this.lightStates = [true, false, false]; // Red only
                this.systemStatus = "ร้อนเกินไป";
                this.systemStatus1 = "แห้ง";
            } else if (tempStatus === 'cold' || humiStatus === 'damp') {
                this.lightStates = [false, false, true]; // Red only
                this.systemStatus = "เย็นเกินไป";
                this.systemStatus1 = "ชื้น";
            }

            // กรณีที่อุณหภูมิหรือความชื้นค่อนข้างเหมาะสม
            else if (tempStatus === 'quite hot' || humiStatus === 'quite dry') {
                this.lightStates = [true, true, false]; // Red and Green
                this.systemStatus = "ค่อนข้างร้อน";
                this.systemStatus1 = "ค่อนข้างแห้ง";
            } else if (tempStatus === 'quite cold' || humiStatus === 'quite damp') {
                this.lightStates = [false, true, true]; // Blue and Green
                this.systemStatus = "ค่อนข้างเย็น";
                this.systemStatus1 = "ค่อนข้างชื้น";
            }
            // กรณีที่ทั้งอุณหภูมิและความชื้นไม่เหมาะสม
            else {
                this.lightStates = [true, true, true]; // All lights on
                this.systemStatus = "ไม่เหมาะสม";
                this.systemStatus1 = "ไม่เหมาะสม";
            }
        },


        cycleLights() {
            if (!this.isOn) return;

            const dataSets = [
                {
                    temp: "27.5",
                    humi: "85.175",
                    state: "1",
                    t1: "27.00",
                    t2: "28.00",
                    h1: "80.25",
                    h2: "90.10",
                },
                {
                    temp: "105.00",
                    humi: "3.175",
                    state: "2",
                    t1: "27.00",
                    t2: "28.00",
                    h1: "80.25",
                    h2: "90.10",
                },
                {
                    temp: "0.00",
                    humi: "100.175",
                    state: "3",
                    t1: "27.00",
                    t2: "28.00",
                    h1: "80.25",
                    h2: "90.10",
                },
                {
                    temp: "28.00",
                    humi: "81.175",
                    state: "4",
                    t1: "27.00",
                    t2: "28.00",
                    h1: "80.25",
                    h2: "90.10",
                },
                {
                    temp: "27.60",
                    humi: "87.175",
                    state: "5",
                    t1: "27.5",
                    t2: "28.00",
                    h1: "80.25",
                    h2: "90.10",
                },
                {
                    temp: "100.60",
                    humi: "100.175",
                    state: "6",
                    t1: "27.5",
                    t2: "28.00",
                    h1: "80.25",
                    h2: "90.10",
                },
                // Reset to initial values on the 7th press
            ];

            if (this.stepIndex < dataSets.length) {
                const data = dataSets[this.stepIndex];
                // this.client.publish("temperature", JSON.stringify(data));
                this.value_temp = parseFloat(data.temp);
                this.value_humi = parseFloat(data.humi);
                this.value_state = data.state;
                this.value_t1 = parseFloat(data.t1);
                this.value_t2 = parseFloat(data.t2);
                this.value_h1 = parseFloat(data.h1);
                this.value_h2 = parseFloat(data.h2);
                this.updateLightStates();
                this.stepIndex++;
            } else {
                this.resetValues();
                this.stepIndex = 0; // Reset the step index
            }
        },
        getButtonColor(index) {
            return this.isOn ? "#FCDE70" : "#EEE";
        },
        getButtonLabel(index) {
            // Returns button label based on index
            if (index === 6) return 'Turn OFF';
            if (index === 5) return 'Turn All ';
            if (index === 4) return 'switch 4';
            if (index === 3) return 'switch 3';
            if (index === 2) return 'switch 2';
            if (index === 1) return 'switch 1';
            return '';
        },

    },
};
</script>

<style scoped>
.text-center {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: center;
  gap: 20px;
}

.rectangular-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  border: 3px solid #E8DBD4;
  border-radius: 25px;
  padding: 20px;
  background-color: #ffffff;
  box-shadow: 0 6px 12px rgba(0, 0, 0, 0.1);
  width: 300px;
  height: 200px;
  text-align: center;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.rectangular-container:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.2);
}

.text-content {
  margin-bottom: 10px;
  color: #2A505A;
}

.text-content h3 {
  margin: 0;
  font-size: 16px;
  font-weight: bold;
}

.text-content p {
  margin: 0;
  font-size: 14px;
  color: #555;
}

.v-btn {
  font-size: 16px;
  transition: background-color 0.3s ease;
}

.v-btn:hover {
  background-color: #2A505A;
  color: #ffffff;
}
</style>