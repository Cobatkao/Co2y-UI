<template>
    <div class="toast" ref="toast">
        <div class="message">
            <slot v-if="!closeButton.enableHTML"></slot>
            <div v-else v-html="$slots.default"></div>
        </div>
        <span class="close_line" ref="closeLine"></span>
        <span class="close_text" v-if="closeButton.closeText" @click="tapClose">{{ closeButton.closeText }}</span>
    </div>
</template>

<script>
  export default {
    name: 'wheel-toast',
    props: {
      autoClose: { // 开启自动关闭
        type: Boolean,
        default: true
      },
      autoCloseDelay: { // 自定义自动关闭时间，默认3000ms
        type: Number,
        default: 3
      },
      closeButton: {
        type: Object,
        default() {
          return {
            closeText: 'Close Me',
            enableHTML: false,
            callback: undefined
          }
        }
      },
    },
    mounted() {
      this.updateLineStyle()
      this.execAutoClose()
    },
    methods: {
      updateLineStyle() {
        this.$nextTick(() => {
          this.$refs['closeLine'].style.height = this.$refs['toast'].getBoundingClientRect().height
        })
      },
      execAutoClose() {
        this.autoClose && setTimeout(() => {
          this.close()
        }, this.autoCloseDelay * 1000)
      },
      close() { // 移除并销毁组件（解绑事件等等 ）
        this.$el.remove()
        this.$destroy()
      },
      tapClose() {
        // 关闭时执行用户自定义回调
        // 如果有回调函数就执行，没有就不执行
        this.closeButton && (typeof this.closeButton.callback === 'function') && this.closeButton.callback(this)
        this.close() // 移除并销毁组件
      }
    }
  }
</script>

<style scoped lang="scss">

    @import '../common.scss';

    .toast {
        display: inline-flex;
        align-items: center;
        position: fixed;
        top: 10px;
        left: 50%;
        transform: translateX(-50%);
        min-height: $toast-height;
        font-size: $toast-fontSize;
        line-height: 1.8;
        color: #ffffff;
        padding: 0 16px;
        background: $toast-bgColor;
        box-shadow: 0 0 3px 0 rgba(0, 0, 0, 0.5);
        border-radius: 3px;

        .message {
            padding: 8px 0;
        }

        .close_line {
            border-left: 1px solid #ffffff;
            margin-left: 10px;
            margin-right: 10px;
        }

        .close_text {
            color: #ffffff;
            font-size: 14px;
            flex-shrink: 0;
        }
    }
</style>