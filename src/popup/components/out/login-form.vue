<template>
  <form>
    <p>Logging in to {{ url }}</p>
    <div class="form-group">
      <label for="username">
        Username
      </label>
      <input id="username" v-model="uname" type="text" class="form-control" placeholder="Your username" />
    </div>
    <div class="form-group">
      <label for="password">
        Password
      </label>
      <input id="password" v-model="pass" type="password" class="form-control" />
    </div>
    <p v-if="errMsg == 'errors.you_cannot_do_that'" class="alert alert-danger">
      Invalid creds
    </p>
    <p v-if="errMsg.length > 0 && errMsg != 'errors.you_cannot_do_that'" class="alert alert-danger">
      {{ errMsg }}
    </p>
    <button v-if="!working" type="submit" class="btn btn-primary w-auto" @click.prevent="submit">
      Sign in
    </button>
    <button v-if="working" type="submit" class="btn btn-primary w-auto" disabled>
      Signing in...
    </button>
  </form>
</template>

<script>
import msgBroker from '@/popup/services/msg-broker'

export default {
  name: 'login-form',
  props: {
    url: String
  },
  data() {
    return {
      working: false,
      errMsg: '',
      uname: '',
      pass: ''
    }
  },
  methods: {
    submit(ev) {
      ev.preventDefault()
      this.working = true
      this.errMsg = ''
      var request = { cmd: 'login', url: this.url + '/api', user: this.uname, pass: this.pass }
      msgBroker.sendToRuntimeAndGet(request, msg => {
        this.working = false
        if ('error' in msg.response) {
          this.errMsg = msg.response.error
          return
        }
        window.close()
      })
    }
  }
}
</script>
