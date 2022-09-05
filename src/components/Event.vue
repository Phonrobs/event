<template>
  <v-container>
    <v-card v-if="!isLogin">
      <v-card-text>
        <v-icon large>mdi-account</v-icon>
        Please click login button.
      </v-card-text>
      <v-card-actions class="justify-center">
        <v-btn color="primary" @click="login">Login</v-btn>
      </v-card-actions>
    </v-card>

    <v-card v-else>
      <v-card-title>เพิ่มกิจกรรม</v-card-title>
      <v-card-text>
        <v-row>
          <v-col cols="12">
            <v-text-field
              label="Subject"
              outlined
              dense
              v-model="eventSubject"
              hide-details
            ></v-text-field
          ></v-col>
          <v-col cols="12">
            <label class="v-label">Start</label>
            <v-date-picker elevation="1" v-model="eventStart"></v-date-picker>
          </v-col>
          <v-col cols="12">
            <label class="v-label">End</label>
            <v-date-picker elevation="1" v-model="eventEnd"></v-date-picker>
          </v-col>
        </v-row>
      </v-card-text>

      <v-card-actions class="justify-center">
        <v-btn color="primary" @click="addEvent">เพิ่ม</v-btn>
      </v-card-actions>
    </v-card>

    <v-snackbar v-model="success"> ทำการเพิ่มกิจกรรมเรียบร้อยแล้ว </v-snackbar>
  </v-container>
</template>

<script>
import auth from "@/auth.js";
import client from "@/graph.js";

export default {
  name: "EventComponent",

  data() {
    return {
      isLogin: false,
      eventSubject: null,
      eventStart: null,
      eventEnd: null,
      success: false,
    };
  },

  async mounted() {
    const account = auth.getAccountInfo();

    if (account != null) this.isLogin = true;
  },

  methods: {
    async login() {
      const result = await auth.login();

      if (result != null) {
        this.isLogin = true;
      }
    },
    async addEvent() {
      if (
        this.eventSubject == null ||
        this.eventStart == null ||
        this.eventEnd == null
      ) {
        alert("โปรดกรอกข้อมูลก่อน!");
        return;
      }

      const start = new Date(this.eventStart);
      const end = new Date(this.eventEnd);

      const event = {
        subject: this.eventSubject,
        start: {
          dateTime: start.toISOString(),
          timeZone: "UTC",
        },
        end: {
          dateTime: end.toISOString(),
          timeZone: "UTC",
        },
      };

      try {
        await client.api("/me/events").post(event);
        this.success = true;
      } catch (err) {
        alert(err);
      }
    },
  },
};
</script>

<style scoped>
label.v-label {
  display: block;
  padding-bottom: 10px;
}
</style>