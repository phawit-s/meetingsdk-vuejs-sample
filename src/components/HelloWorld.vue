<script setup>
import { ref } from "vue";
import { ZoomMtg } from "@zoom/meetingsdk";

ZoomMtg.preLoadWasm();
ZoomMtg.prepareWebSDK();

var authEndpoint = "http://localhost:3000/zoom/join";
var sdkKey = "OHloeRhFsKmTbK81MJCjucOPgId950rDuyyk";
var passWord = "";
var role = 2;
var registrantToken = "";
var zakToken = "";
var leaveUrl = "http://localhost:5173/";

const meetingNumber = ref("");
const userName = ref("");
const userEmail = ref("");

function getSignature() {
  if (!meetingNumber.value || !userName.value || !userEmail.value) {
    alert("กรุณากรอกข้อมูลให้ครบ");
    return;
  }

  fetch(authEndpoint, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({
      meetingId: meetingNumber.value,
      role: 1,
    }),
  })
    .then((response) => response.json())
    .then((data) => {
      console.log(data);
      startMeeting(data.token, data.password);
    })
    .catch((error) => console.log(error));
}

function startMeeting(signature, password) {
  document.getElementById("zmmtg-root").style.display = "block";
  console.log(signature, password);
  
  ZoomMtg.init({
    leaveUrl: leaveUrl,
    patchJsMedia: true,
    leaveOnPageUnload: true,
    success: () => {
      ZoomMtg.join({
        signature: signature,
        sdkKey: sdkKey,
        meetingNumber: meetingNumber.value,
        passWord: password,
        userName: userName.value,
        userEmail: userEmail.value,
        // tk: registrantToken,
        // zak: zakToken,
        success: (success) => console.log(success),
        error: (error) => console.log(error),
      });
    },
    error: (error) => console.log(error),
  });
}
</script>

<template>
  <div>
    <h1>Zoom Meeting SDK Vue.js Sample</h1>
    <div class="d-flex justify-center items-center flex-col">
      <input
        v-model="meetingNumber"
        placeholder="Enter Meeting ID"
        class="mb-4 p-2 border rounded"
      />
      <input
        v-model="userName"
        placeholder="Enter Username"
        class="mb-4 p-2 border rounded"
      />
      <input
        v-model="userEmail"
        placeholder="Enter Email"
        type="email"
        class="mb-4 p-2 border rounded"
      />
    </div>
    <button
      @click="getSignature"
      class="p-3 bg-blue-500 text-white rounded hover:bg-blue-700"
    >
      Join Meeting
    </button>
  </div>
</template>

<style scoped>
input {
  display: block;
  margin-bottom: 10px;
  padding: 8px;
  width: 100%;
  max-width: 300px;
}
button {
  padding: 10px;
  background-color: #007bff;
  color: white;
  border: none;
  cursor: pointer;
}
button:hover {
  background-color: #0056b3;
}
</style>
