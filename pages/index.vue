<template>
  <div>
    <a id="kakao-login-btn" @click="loginWithKakao">
      <img src="https://k.kakaocdn.net/14/dn/btroDszwNrM/I6efHub1SN5KCJqLm1Ovx1/o.jpg" width="222"
           alt="카카오 로그인 버튼" />
    </a>
    <p id="token-result"></p>
  </div>
</template>

<script setup>
import { onMounted } from 'vue'

// <script src="https://t1.kakaocdn.net/kakao_js_sdk/2.7.2/kakao.min.js"
// integrity="sha384-TiCUE00h649CAMonG018J2ujOgDKW/kVWlChEuu4jK2vxfAAD0eZxzCKakxg55G4" crossorigin="anonymous">
//




const loginWithKakao =()=> {
  Kakao.Auth.authorize({
    redirectUri: 'http://localhost:3000/', // 로그인 후 이동될 페이지
  });
}

// 아래는 데모를 위한 UI 코드입니다.
function displayToken() {
  const token = getCookie('authorize-access-token');

  if(token) {
    Kakao.Auth.setAccessToken(token);
    try{
      const res =  Kakao.Auth.getStatusInfo()
      if (res.status === 'connected') {
        document.getElementById('token-result').innerText
          = 'login success, token: ' + Kakao.Auth.getAccessToken();
      }
    }catch(err) {
      Kakao.Auth.setAccessToken(null);
    }
  }
}

function getCookie(name) {
  const parts = document.cookie.split(name + '=');
  if (parts.length === 2) { return parts[1].split(';')[0]; }
}

onMounted(()=>{
  Kakao.init('5f8223a41a4fce39316486da5b0dc8ae');
  displayToken()
})
</script>
