<template>
  <StackLayout v-if="visible" class="message" :class="className">
    <Button
      v-if="allowDismiss"
      class="dismiss-button"
      :text="Dismiss"
      @tap="dismiss"
    />
    <Label class="h1" v-if="header" :text="header"/>
    <slot/>
  </StackLayout>
</template>

<script>
  export default {
    name: 'Message',
    props: {
      header: {
        type: String,
        required: false,
      },
      visible: {
        type: Boolean,
        default: true,
      },
      allowDismiss: {
        type: Boolean,
        default: false,
      },
    },
    computed: {
      hasHeader() {
        return this.$slots.header
      },
      className() {
        return `${this.variant} ${this.hasHeader ? 'has-header' : ''}`
      },
    },
    methods: {
      dismiss() {
        this.$emit('dismiss')
      },
    },
  }
</script>

<style lang="scss" scoped>
  .message {
    font-size: 15;
    background: tomato;
    color: white;
  }

  .h1 {
    font-size: 30;
  }
</style>
