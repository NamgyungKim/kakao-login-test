<template>
  <div>
    <form style="border: 1px solid black; padding: 10px">
      <p>1. url 로 url 데이터 가져오기</p>
      <button type="button" @click="getUrl">getUrl</button>
    </form>
    <form style="border: 1px solid black; padding: 10px">
      <p>2. 가져온 url 데이터로 file 넣기</p>
      <p>
        <label>file</label>
        <input ref="$file" type="file" />
      </p>
      <p>
        <label>url</label>
        <input v-model="url" />
      </p>
      <p>
        <label>bucket</label>
        <input v-model="bucket" />
      </p>
      <p>
        <label>XAmzAlgorithm</label>
        <input v-model="XAmzAlgorithm" />
      </p>
      <p>
        <label>XAmzCredential</label>
        <input v-model="XAmzCredential" />
      </p>
      <p>
        <label>XAmzDate</label>
        <input v-model="XAmzDate" />
      </p>
      <p>
        <label>key</label>
        <input v-model="key" />
      </p>
      <p>
        <label>Policy</label>
        <input v-model="Policy" />
      </p>
      <p>
        <label>XAmzSignature</label>
        <input v-model="XAmzSignature" />
      </p>
      <p>
        <label>ContentType</label>
        <input v-model="ContentType" />
      </p>
      <button type="button" @click="getPath">submit</button>
    </form>
  </div>
</template>
<script setup lang="ts">
import { ref } from 'vue'

const $file = ref<HTMLInputElement>()

const url = ref('')
const bucket = ref('')
const XAmzAlgorithm = ref('')
const XAmzCredential = ref('')
const XAmzDate = ref('')
const key = ref('')
const Policy = ref('')
const XAmzSignature = ref('')
const ContentType = ref('')

const getUrl = async () => {
  await fetch('http://localhost:3001/v1/user/image/sign-url', {
    headers: {
      Authorization: `Bearer `,
      'Content-Type': 'application/json'
    }
  }).then(function(response) {
    return response.json()
  })
    .then(function({ data }) {
      url.value = data.url
      bucket.value = data.fields.bucket
      XAmzAlgorithm.value = data.fields['X-Amz-Algorithm']
      XAmzCredential.value = data.fields['X-Amz-Credential']
      XAmzDate.value = data.fields['X-Amz-Date']
      key.value = data.fields.key
      Policy.value = data.fields.Policy
      XAmzSignature.value = data.fields['X-Amz-Signature']
      ContentType.value = 'image/jpg'
    })
}
const getPath = () => {
  const file = $file.value?.files

  const form = new FormData()

  if (file && file.length > 0) {
    form.append('bucket', bucket.value)
    form.append('X-Amz-Algorithm', XAmzAlgorithm.value)
    form.append('X-Amz-Credential', XAmzCredential.value)
    form.append('X-Amz-Date', XAmzDate.value)
    form.append('key', key.value)
    form.append('Policy', Policy.value)
    form.append('X-Amz-Signature', XAmzSignature.value)
    form.append('Content-Type', ContentType.value)
    form.append('file', file[0])


    fetch(url.value, {
      method: 'post',
      body: form
    })
  }
}

</script>
<style scoped>

</style>
