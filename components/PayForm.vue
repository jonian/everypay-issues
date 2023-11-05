<template>
  <form class="container">
    <label>
      Theme
      <select v-model="options.theme">
        <option
          v-for="item in themes"
          :key="item"
          :value="item">
          {{ item }}
        </option>
      </select>
    </label>
    <label>
      Size
      <select v-model="options.size">
        <option
          v-for="item in sizes"
          :key="item"
          :value="item">
          {{ item }}
        </option>
      </select>
    </label>
    <label>
      Width
      <input
        v-model="options.width"
        style="width: 80px"
        type="number" />
    </label>
    <label>
      Button
      <input
        v-model="options.button"
        type="checkbox" />
    </label>
    <label>
      Errors
      <input
        v-model="options.errors"
        type="checkbox" />
    </label>
    <label>
      Full Width
      <input
        v-model="fullWidth"
        type="checkbox" />
    </label>
    <span class="payload-toggle">
      <span @click="showPayload = !showPayload">
        {{ showPayload ? 'Hide' : 'Show' }} payload
      </span>
    </span>
  </form>
  <div
    v-if="showPayload"
    class="container info-box">
    <h3>Payload</h3>
    <pre>{{ payload }}</pre>
  </div>
  <div class="container info-box">
    <h3>Callbacks</h3>
    <pre>{{ callbacks }}</pre>
  </div>
  <div
    :style="{ width }"
    class="form-wrapper">
    <div
      :class="{ fullWidth }"
      id="payment-form"
      ref="form" />
    <button
      :disabled="!formLoaded"
      @click="onSubmit">
      External pay button
    </button>
  </div>
</template>

<script>
export default {
  watch: {
    options: {
      handler: 'createForm',
      deep: true
    }
  },
  data() {
    return {
      themes: [
        'default', 'material', 'barebone'
      ],
      sizes: [
        'xs', 'sm', 'lg', 'xl'
      ],
      options: {
        theme: 'barebone',
        size: 'xl',
        width: 1200,
        button: true,
        errors: true
      },
      callbacks: {
        response: {},
        validation: {},
        errors: []
      },
      formLoaded: false,
      showPayload: false,
      payload: null,
      fullWidth: false
    }
  },
  computed: {
    width() {
      return `${this.options.width}px`
    }
  },
  methods: {
    createForm() {
      this.formLoaded = false

      while (this.$refs.form.lastChild) {
        this.$refs.form.removeChild(this.$refs.form.lastChild)
      }

      const payload = {
        elementId: 'payment-form',
        pk: 'pk_59iDJJlD6yoZs7Z7Pxxugg1bh6aKS1iM',
        amount: 50,
        locale: 'el',
        theme: this.options.theme,
        display: {
          button: this.options.button,
          errors: this.options.errors
        },
        formOptions: {
          background: 'transparent',
          padding: 0,
          borderRadius: 0,
          size: this.options.size
        }
      }

      this.payload = { ...payload, pk: '******' }

      everypay.payform(payload, this.onResponse, this.onValidate, this.onError)
    },
    onValidate(resp) {
      this.callbacks.validation = resp
    },
    onError(resp) {
      this.callbacks.errors = resp
    },
    onResponse(resp) {
      if (resp.onLoad) {
        this.formLoaded = true
      }

      this.callbacks.response = resp
    },
    onSubmit() {
      everypay.onClick()
    }
  },
  mounted() {
    this.createForm()
  }
}
</script>

<style lang="scss">
.form-wrapper {
  display: block;
  border: 1px solid black;
  width: 1200px;
  padding: 20px;
  box-sizing: border-box;

  button {
    width: 100%;
    margin-top: 20px;
    padding: 10px;
    text-align: center;
    background: black;
    color: white;
    border: none;
    font-weight: bold;
    cursor: pointer;
    transition: opacity 0.5s;

    &:disabled {
      opacity: 0.5;
      cursor: not-allowed;
    }
  }
}

#payment-form {
  border: 1px solid red;
  position: relative;

  &.fullWidth {
    width: 100% !important;

    iframe {
      width: 100% !important;
    }
  }
}

.info-box {
  position: relative;
  z-index: 0;
  overflow: hidden;

  h3 {
    position: absolute;
    font-size: 14px;
    top: -1px;
    right: -1px;
    padding: 5px 10px;
    line-height: 1;
    background: #ddd;
    z-index: 1;
    color: #555;
  }

  pre {
    position: relative;
    z-index: 0;
  }
}

pre {
  background: #fafafa;
  border: 1px solid #ddd;
  padding: 20px;
  overflow-x: auto;
}

form {
  display: flex;
  padding: 20px;
  border: 1px solid #ddd;
  gap: 15px;
  align-items: center;
  overflow-x: auto;

  label {
    display: flex;
    align-items: center;
    gap: 5px;
    white-space: nowrap;
  }

  .payload-toggle {
    flex-grow: 1;
    text-align: right;
    cursor: pointer;
    text-decoration: underline;
    text-decoration-style: dotted;
    color: #555;
    white-space: nowrap;
  }
}
</style>
