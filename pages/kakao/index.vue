<template>
  <div>
    <a id="kakao-login-btn" @click="loginWithKakao">
      <img src="https://k.kakaocdn.net/14/dn/btroDszwNrM/I6efHub1SN5KCJqLm1Ovx1/o.jpg" width="222"
           alt="카카오 로그인 버튼" />
    </a>
    <button @click="reissue">Reissue</button>
    <button @click="unlink">탈퇴</button>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue'

// 프론트 측에서 봐야할 docs : https://developers.kakao.com/docs/latest/ko/kakaologin/js#login
// 상태값
const accessToken = ref('')
const refreshToken = ref('')
const expiresIn = ref(null)
const refreshTokenExpiresIn = ref(null)

const loginWithKakao = () => {
  Kakao.Auth.authorize({
    redirectUri: 'http://localhost:3001/v1/user/kakao-login',
    scope: 'profile_nickname,profile_image'
  })
}

// 아래는 데모를 위한 UI 코드입니다.
function displayToken() {
  const token = getCookie('authorize-access-token')

  if (token) {
    Kakao.Auth.setAccessToken(token)
    try {
      const res = Kakao.Auth.getStatusInfo()
      if (res.status === 'connected') {
        document.getElementById('token-result').innerText
          = 'login success, token: ' + Kakao.Auth.getAccessToken()
      }
    } catch (err) {
      Kakao.Auth.setAccessToken(null)
    }
  }
}

function getCookie(name) {
  const parts = document.cookie.split(name + '=')
  if (parts.length === 2) {
    return parts[1].split(';')[0]
  }
}

function reissue() {
  fetch('http://localhost:3001/v1/user/reissue', {
    headers: {
      Authorization: `Bearer ${accessToken.value}`,
      'Content-Type': 'application/json'
    }
  }).then((response) => response.text())
}

function unlink() {
  fetch('http://localhost:3001/v1/user/unlink', {
    headers: {
      Authorization: `Bearer ${accessToken.value}`,
      'Content-Type': 'application/json'
    }
  }).then((response) => response.text())
}

// useEffactor -> 라이프 사이클 초기
onMounted(() => {
  Kakao.init('3bb476a10a8680e4906f9ecc74b2ed4a')
  displayToken()

  if (getCookie('pk')) {
    const pk = JSON.parse(getCookie('pk'))
    accessToken.value = pk.accessToken
    refreshToken.value = pk.refreshToken
    expiresIn.value = pk.expiresIn
    refreshTokenExpiresIn.value = pk.refreshTokenExpiresIn
  }
})
</script>
