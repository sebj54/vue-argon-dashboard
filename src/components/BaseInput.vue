<template>
    <div class="form-group"
         :class="[
       {'has-danger': error},
       {'focused': focused},
       {'has-label': label || $slots.label},
       {'has-success': valid === true},
       {'has-danger': valid === false}
       ]">
        <slot name="label" v-bind="slotData">
            <label v-if="label" class="form-control-label" :class="labelClasses" :for="inputId">
                {{label}}
                <span v-if="required">*</span>
            </label>
        </slot>

        <div :class="{'input-group': hasIcon}">
          <div v-if="addonLeftIcon || $slots.addonLeft" class="input-group-prepend">
            <slot name="addonLeft">
              <span class="input-group-text">
                  <i :class="addonLeftIcon"></i>
              </span>
            </slot>
          </div>
          <slot v-bind="slotData">
              <input
                      :id="inputId"
                      :value="value"
                      v-on="listeners"
                      v-bind="$attrs"
                      class="form-control"
                      :class="[
                      {'is-valid': valid === true},
                      {'is-invalid': valid === false}, inputClasses]"
                      aria-describedby="addon-right addon-left">
          </slot>
          <div v-if="addonRightIcon || $slots.addonRight" class="input-group-append">
            <slot name="addonRight">
              <span class="input-group-text">
                <i :class="addonRightIcon"></i>
              </span>
            </slot>
          </div>
        </div>

        <slot name="infoBlock"></slot>
        <slot name="helpBlock">
            <div class="text-danger invalid-feedback" style="display: block;" :class="{'mt-2': hasIcon}" v-if="error">
                {{ error }}
            </div>
        </slot>
    </div>
</template>
<script>
export default {
  inheritAttrs: false,
  name: "base-input",
  props: {
    required: {
      type: Boolean,
      description: "Whether input is required (adds an asterix *)"
    },
    valid: {
      type: Boolean,
      description: "Whether is valid",
      default: undefined
    },
    label: {
      type: String,
      description: "Input label (text before input)"
    },
    error: {
      type: String,
      description: "Input error (below input)"
    },
    labelClasses: {
      type: String,
      description: "Input label css classes"
    },
    inputClasses: {
      type: String,
      description: "Input css classes"
    },
    value: {
      type: [String, Number],
      description: "Input value"
    },
    addonRightIcon: {
      type: String,
      description: "Addon right icon"
    },
    addonLeftIcon: {
      type: String,
      description: "Addont left icon"
    }
  },
  data() {
    return {
      focused: false
    };
  },
  computed: {
    listeners() {
      return {
        ...this.$listeners,
        input: this.updateValue,
        focus: this.onFocus,
        blur: this.onBlur
      };
    },
    slotData() {
      return {
        focused: this.focused,
        inputId: this.inputId,
        ...this.listeners
      };
    },
    hasIcon() {
      const { addonRight, addonLeft } = this.$slots;
      return (
        addonRight !== undefined ||
        addonLeft !== undefined ||
        this.addonRightIcon !== undefined ||
        this.addonLeftIcon !== undefined
      );
    },
    inputId() {
      return 'base-input-' + this._uid
    }
  },
  methods: {
    updateValue(evt) {
      let value = evt.target.value;
      this.$emit("input", value);
    },
    onFocus(value) {
      this.focused = true;
      this.$emit("focus", value);
    },
    onBlur(value) {
      this.focused = false;
      this.$emit("blur", value);
    }
  }
};
</script>
<style>
</style>
