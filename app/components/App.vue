<template>
  <Page>
    <ActionBar title="Welcome to NativeScript-Vue!"/>
    <StackLayout class="login-form">
      <Label class="heading" text="Select network request tool"/>
      <ListPicker :items="networkRequestTools" v-model="networkRequestToolIndex"/>
      <Label v-if="isLoading" text="Loading..."/>
      <ErrorMessage v-if="loginError" :error="loginError" :allow-dismiss="true" @dismiss="clearLoginError"/>
      <Label class="heading" text="Device name"/>
      <Label class="message" :text="deviceName"/>
      <Label class="heading" text="Username"/>
      <TextField
        class="login-input"
        v-model="username"
        keyboardType="email"
        returnKeyType="next"
        @returnPress="focusPasswordInput"
      />
      <Label class="heading" text="Password"/>
      <TextField
        ref="passwordInput"
        class="login-input"
        :secure="true"
        v-model="password"
        returnKeyType="go"
        @returnPress="onLoginFormSubmit"
      />
      <Button text="Submit" @tap="onLoginFormSubmit"/>
    </StackLayout>
  </Page>
</template>

<script>
  import ErrorMessage from "./ErrorMessage.vue";

  const {device} = require('tns-core-modules/platform')
  const http = require('tns-core-modules/http')

  export default {
    name: 'App',
    components: {
      ErrorMessage
    },
    data() {
      let deviceName = `${device.manufacturer} ${device.model}`

      if (device.manufacturer !== 'Apple') {
        deviceName = `${device.manufacturer} ${device.model} ${device.deviceType}`
      }

      return {
        networkRequestToolIndex: 0,
        networkRequestTools: ['fetch', 'http'],
        loginError: null,
        username: '',
        password: '',
        isLoading: false,
        deviceName,
      }
    },
    computed: {
      networkRequestTool() {
        return this.networkRequestTools[this.networkRequestToolIndex]
      }
    },
    methods: {
      clearLoginError() {
        this.loginError = null
      },
      focusPasswordInput() {
        this.$refs.passwordInput.nativeView.focus()
      },
      onLoginFormSubmit() {
        const headers = {'Content-Type': 'application/json'}
        const method = 'POST'
        const payload = {username: this.username, password: this.password, deviceName: this.deviceName}

        this.isLoading = true

        console.log(`Trying to post with ${this.networkRequestTool}`)

        switch (this.networkRequestTool) {
          case 'fetch':
            fetch('https://httpbin.org/post', {
              method,
              headers,
              body: JSON.stringify(payload),
            })
              .then(response => response.json())
              .then(this.onNetworkResponse, this.onNetworkError)
              .finally(() => {
                this.isLoading = false
              })
            break;
          case 'http':
            http.request({
              url: "https://httpbin.org/post",
              method,
              headers,
              content: JSON.stringify(payload)
            })
              .then(response => response.json())
              .then(this.onNetworkResponse, this.onNetworkError)
              .finally(() => {
                this.isLoading = false
              })
            break;
          default:
            console.warn(`${this.networkRequestTool} wasn't found`)
            break;
        }

      },
      onNetworkResponse(data) {
        console.log('Received', data)
      },
      onNetworkError(error) {
        console.error('Network error', error)
        this.loginError = error
      }
    },
  }
</script>

<style scoped>
  ActionBar {
    background-color: #53ba82;
    color: #ffffff;
  }

  .login-form {
    font-size: 16;
    color: black;
    margin: 16;
  }

  .heading, .message {
    margin: 8 0;
  }

  .heading {
    font-weight: bold;
  }
</style>
