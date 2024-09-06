<template>
    <br>
    <v-responsive max-width="100%" class="background">
        <div class="container">
            <v-row class="text-center">
                <!-- Temperature Card -->
                <v-col cols="12" sm="12" md="12">
                    <v-card class="mx-auto" max-width="800" hover
                        style="border-radius: 15px; background-color: rgba(255, 255, 255, 0.5); color:#fff;">
                        <v-card-title>
                            <v-col cols="12">
                                <h2 class="text-center">อุณหภูมิและความชื้น</h2>
                            </v-col>
                        </v-card-title>
                        <v-card-text>
                            <v-row class="justify-center">
                                <!-- Temperature -->
                                <v-col cols="auto" class="text-center">
                                    <h2>อุณหภูมิ</h2>
                                    <v-progress-circular :model-value="value_temp" :rotate="360" :size="100" :width="16"
                                        color="red">
                                        <template v-slot:default> {{ value_temp }} °C </template>
                                    </v-progress-circular>
                                </v-col>

                                <!-- Humidity -->
                                <v-col cols="auto" class="text-center">
                                    <h2>ความชื้น</h2>
                                    <v-progress-circular :model-value="value_humi" :rotate="360" :size="100" :width="16"
                                        color="blue">
                                        <template v-slot:default> {{ value_humi }} % </template>
                                    </v-progress-circular>
                                </v-col>

                                <!-- Optimal Temperature -->
                                <v-col cols="auto" class="text-center">
                                    <h2>อุณหภูมิที่เหมาะสม</h2>
                                    <v-progress-circular :model-value="totalValueT" :rotate="360" :size="100"
                                        :width="16" color="red">
                                        <template v-slot:default> {{ totalValueT }} °C </template>
                                    </v-progress-circular>
                                    <v-row class="mt-4">
                                        <v-col cols="6" class="text-center">
                                            <div>ต่ำสุด</div>
                                            <div>{{ value_t1 }} °C</div>
                                        </v-col>
                                        <v-col cols="6" class="text-center">
                                            <div>ไม่เกิน</div>
                                            <div>{{ value_t2 }} °C</div>
                                        </v-col>
                                    </v-row>
                                </v-col>

                                <!-- Optimal Humidity -->
                                <v-col cols="auto" class="text-center">
                                    <h2>ความชื้นที่เหมาะสม</h2>
                                    <v-progress-circular :model-value="totalValueH" :rotate="360" :size="100"
                                        :width="16" color="blue">
                                        <template v-slot:default> {{ totalValueH }} % </template>
                                    </v-progress-circular>
                                    <v-row class="mt-4">
                                        <v-col cols="6" class="text-center">
                                            <div>ต่ำสุด</div>
                                            <div>{{ value_h1 }} %</div>
                                        </v-col>
                                        <v-col cols="6" class="text-center">
                                            <div>ไม่เกิน</div>
                                            <div>{{ value_h2 }} %</div>
                                        </v-col>
                                    </v-row>
                                </v-col>
                            </v-row>
                        </v-card-text>

                        <v-card-text>
                            <v-row>
                                <v-col cols="12">

                                    <v-row>
                                        <v-col cols="6">
                                            <h2>อุณหภูมิ</h2>
                                            <h4 style="color: red;">{{ value_temp }} C</h4>
                                            <h2 style="font-size: 20px;">อยู่ในระดับที่ {{ systemStatus }}</h2>
                                        </v-col>
                                        <v-col cols="6">
                                            <h2>ความชื้น</h2>
                                            <h4 style="color: blue;">{{ value_humi }} PH</h4>
                                            <h2>อยู่ในระดับที่ {{ systemStatus1 }}</h2>
                                        </v-col>
                                    </v-row>
                                </v-col>
                            </v-row>
                        </v-card-text>

                        <v-col>

                            <v-card-text>
                                <v-row>
                                    <v-col cols="12">
                                        <v-row class="d-flex align-center justify-center">
                                            <v-col cols="4">
                                                <div class="rounded-circle mx-auto"
                                                    :style="{ height: '64px', width: '64px', backgroundColor: lightStates[0] ? 'red' : '#ddd' }">
                                                </div>
                                                <div>ความร้อน</div>
                                            </v-col>
                                            <v-col cols="4">
                                                <div class="rounded-circle mx-auto"
                                                    :style="{ height: '64px', width: '64px', backgroundColor: lightStates[1] ? 'green' : '#ddd' }">
                                                </div>
                                                <div>ปกติ</div>
                                            </v-col>
                                            <v-col cols="4">
                                                <div class="rounded-circle mx-auto"
                                                    :style="{ height: '64px', width: '64px', backgroundColor: lightStates[2] ? 'blue' : '#ddd' }">
                                                </div>
                                                <div>เย็น</div>
                                            </v-col>
                                        </v-row>
                                        <h1>{{ systemStatus }}</h1> 
                                    </v-col>
                                </v-row>
                            </v-card-text>
                            <v-row class="d-flex align-center justify-center">
                                        <v-col cols="2">
                                            <br><br>
                                            <div class="rounded-circle mx-auto"
                                                :style="{ height: '20px', width: '20px', background: isOn ? 'yellow' : '#EEE' }">
                                            </div>
                                            ON
                                        </v-col>
                                        <v-col cols="2">
                                            <br><br>
                                            <div class="rounded-circle mx-auto"
                                                :style="{ height: '20px', width: '20px', background: !isOn ? 'red' : '#EEE' }">
                                            </div>
                                            OFF
                                        </v-col>
                                    </v-row>
                        </v-col>
                        <v-btn @click="cycleLights" class="mt-2" :style="{ backgroundColor: getButtonColor(index) }"
                            block :disabled="!isOn">
                            {{ stepButtonLabel }}
                        </v-btn>
                        <v-row>
                            <v-col cols="6" sm="6">
                                <v-btn block style="background-color: yellow;color: #fff;height: 60px;"
                                    @click="start">Start</v-btn>
                            </v-col>
                            <br>
                            <v-col cols="6" sm="6">
                                <v-btn block style="background-color: red;color: #fff;height: 60px;"
                                    @click="stop">Stop</v-btn>
                            </v-col>
                        </v-row>
                    </v-card>
                </v-col>
            </v-row>
        </div>
        <br><br>
        <!-- </v-parallax> -->
    </v-responsive>
</template>
<script>
import mqtt from "mqtt";
definePageMeta({
    layout: "iott",
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
            this.client.subscribe('punyaphat');
            this.client.subscribe('moisture');
        },
        onMqttMessage(topic, message) {
            if (topic === 'status') {
                this.msg = message.toString();
            }
            if (topic === 'punyaphat' && this.isOn) {
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
.bulb-wrapper {
    position: relative;
    display: flex;
    flex-direction: column;
    align-items: center;
}

.bulb {
    width: 100px;
    height: 150px;
    border-radius: 50% 50% 0 0;
    background-color: #ddd;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
}

.bulb-base {
    width: 80px;
    height: 30px;
    background-color: #ebe5e5;
    border-radius: 0 0 15px 15px;
    margin-top: -10px;
}

.background {
    background-image: url('https://4kwallpapers.com/images/walls/thumbs_3t/10865.png');
    /* Optional: Add a background image */
    background-size: cover;
    background-position: center;
    background-repeat: no-repeat;
}
</style>
