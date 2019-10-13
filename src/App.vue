<template>
  <v-app>
    <v-content>
      <v-row
        class="ma-5"
        justify="center"
      >
        <v-col
          cols="12"
          md="8"
          lg="6"
        >
          <v-card :elevation="24">
            <v-row class="pa-6">
              <v-col
                cols="12"
                md="5"
                lg="4"
              >
                <v-img
                  src="@/assets/mangosteen.jpg"
                  position="center center"
                  height="100%"
                >
                </v-img>
              </v-col>
              <v-col
                cols="12"
                md="7"
                lg="8"
              >
                <div class="title">
                  มังคุดออร์แกนิก ออนไลน์
                </div>
                <v-form
                  ref="form"
                  action=""
                  v-model="valid"
                  lazy-validation
                >
                  <v-text-field
                    v-model="name"
                    :counter="280"
                    :rules="nameRules"
                    label="ชื่อ-นามสกุล ผู้จอง"
                    required
                  ></v-text-field>

                  <v-text-field
                    v-model="phone"
                    :rules="phoneRules"
                    label="เบอร์โทรศัพท์"
                    hint="ไม่ต้องเติม - ในเบอร์โทรศัพท์"
                    required
                  ></v-text-field>

                  <v-select
                    v-model="selectedPackage"
                    :items="packages"
                    :rules="[v => !!v || 'กรุณาเลือกจำนวนที่ต้องการจอง']"
                    label="จำนวนที่ต้องการจอง"
                    required
                  ></v-select>

                  <v-row
                    class="px-5"
                    justify="space-between"
                  >
                    <v-btn
                      color="error"
                      @click="reset"
                      :loading="loading"
                    >
                      <v-icon left>mdi-delete</v-icon>
                      ล้างข้อมูล
                    </v-btn>

                    <v-btn
                      color="success"
                      @click="submit"
                      :loading="loading"
                    >
                      <v-icon left>mdi-shopping</v-icon>
                      สั่งจองเลย
                    </v-btn>
                  </v-row>
                </v-form>
              </v-col>
            </v-row>
          </v-card>
        </v-col>
        <v-snackbar v-model="snackbar">
          {{ alertMessage }}
          <v-btn
            color="pink"
            text
            @click="snackbar = false"
          >
            ปิด
          </v-btn>
        </v-snackbar>
      </v-row>
    </v-content>
  </v-app>
</template>

<script>
import qs from 'querystring';

export default {
  name: 'App',
  data: () => ({
    loading: false,
    valid: true,
    name: '',
    snackbar: false,
    alertMessage: '',
    nameRules: [
      v => !!v || 'กรุณากรอกชื่อผู้จอง',
      v => (v && v.length <= 280) || 'ชื่อไม่ควรเกิน 280 ตัวอักษร',
    ],
    phone: '',
    phoneRules: [
      v => !!v || 'กรุณาระบุเบอร์โทรศัพท์ของท่าน',
      v => (/\d{9,10}/.test(v) && v.length <= 10) || 'เบอร์โทรศัพท์ไม่ถูกต้อง',
    ],
    selectedPackage: null,
    packages: ['5 กก.', '10 กก.', '15 กก.', '20 กก.'],
  }),
  methods: {
    reset() {
      this.$refs.form.reset();
    },
    async submit() {
      if (this.$refs.form.validate()) {
        const url =          'https://docs.google.com/forms/d/e/1FAIpQLSeRSWxqaW7BvoF47cl_muvGTvw39sm7Qt6DNYL0CkxQNVR0Ww/formResponse';
        const body = {
          'entry.1819278497': this.name,
          'entry.1633641327': this.phone,
          'entry.828954501': this.selectedPackage,
        };

        try {
          this.loading = true;
          await this.postData(url, body);
          this.reset();
          this.alertMessage = 'สั่งจองให้เรียบร้อยแล้ว';
        } catch (error) {
          this.alertMessage = error.message;
        } finally {
          this.loading = false;
          this.snackbar = true;
        }
      }
    },
    postData(url, data) {
      return fetch(url, {
        method: 'POST',
        mode: 'no-cors',
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded',
        },
        body: qs.stringify(data),
      });
    },
  },
};
</script>
