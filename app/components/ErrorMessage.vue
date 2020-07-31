<template>
  <Message class="error-message" :header="messageHeader">
    <Label class="error-message-text" :text="error.message" />
    <ErrorMessage
      v-for="subError in error.errors"
      :error="subError"
      :key="JSON.stringify(subError)"
      :is-nested="true"
    />
    <Button
      v-if="retryCallback"
      :text="retryLabel"
      @tap="retryCallback"
    />
  </Message>
</template>

<script>
import Message from './Message'

export default {
  name: 'ErrorMessage',
  components: {  Message },
  props: {
    error: {
      type: [Error, Object],
      required: true,
    },
    heading: {
      type: String,
      required: false,
    },
    isNested: {
      type: Boolean,
      required: false,
      default: false,
    },
    retryCallback: {
      type: Function,
      required: false,
    },
    retryLabel: {
      type: String,
      required: false,
      default() {
        return 'Retry'
      },
    },
    allowDismiss: {
      type: Boolean,
      default: false,
    },
  },
  computed: {
    messageHeader() {
      if (this.isNested) {
        return null
      }

      return this.heading || 'Oops!'
    },
  },
}


</script>

<style scoped>
.error-message {
  font-size: 15;
}

.error-message-text {
  font-size: 15;
}
</style>
