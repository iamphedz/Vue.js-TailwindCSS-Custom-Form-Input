<template>
  <div class="relative flex flex-col mb-6">
    <div
      id="custom-input"
      class="flex flex-row items-center rounded-t px-1 border-b focus-within:bg-gray-200"
      v-bind:class="{
        'border-blue-400': active && inputError.length < 1,
        'border-red-600': active && inputError.length > 0
      }"
    >
      <slot name="leading-icon"></slot>
      <div class="relative flex flex-col w-full">
        <div class="relative flex flex-col p-1">
          <label
            for="input"
            class="this-input-label absolute text-sm z-10 text-gray-500"
            v-bind:class="activeLabel"
            >{{ label }}{{ hasError }}</label
          >
          <div class="flex w-full z-20">
            <input
              v-bind:id="name"
              v-bind:type="type === 'currency' ? 'number' : type"
              v-bind:name="name"
              v-bind:maxlength="maxlength"
              v-bind:step="type === 'currency' ? '.01' : 'any'"
              :required="required"
              class="this-input bg-transparent focus:outline-none mt-6 w-full p-1"
              v-bind:class="{ 'text-red-600': inputError.length > 0 }"
              @focus="active = true"
              @blur="validateOnBlur()"
              @keyup="inputError = ''"
              v-bind:value="value"
              v-on:input="$emit('input', $event.target.value)"
            />
          </div>
        </div>
      </div>
      <slot name="trailing-icon"></slot>
    </div>
    <div class="flex relative">
      <transition name="fade-right">
        <div
          v-if="active && inputError.length < 1"
          class="this-helper absolute top-0 text-gray-600 text-xs font-thin p-1 flex w-full"
        >
          <slot name="helper"></slot>
        </div>
      </transition>
      <transition name="fade-right">
        <div
          v-if="inputError.length > 0"
          class="this-error absolute top-0 text-red-600 text-xs font-thin p-1 flex w-full"
        >
          {{ inputError }}
        </div>
      </transition>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      active: false,
      hasInput: false,
      inputError: ""
    };
  },
  computed: {
    activeLabel() {
      return {
        "text-xs font-semibold mt-0": this.active,
        "text-base mt-8": !this.active && String(this.value).length < 1,
        "text-red-600": this.inputError.length > 0,
        "text-blue-500": this.inputError.length < 1 && this.active,
        "text-gray-500": this.inputError.length < 1 && !this.active
      };
    },
    hasError() {
      return this.inputError.length > 0 ? "*" : "";
    }
  },
  watch: {
    error() {
      this.inputError = this.error ? this.error[0] : "";
    }
  },
  methods: {
    validateOnBlur() {
      this.active = false;
      if (this.inputError.length > 0 && String(this.value).length > 0) {
        this.inputError = "";
      }
    }
  },
  props: {
    name: {
      type: String,
      default() {
        return Math.random()
          .toString(36)
          .substr(2);
      }
    },
    label: {
      type: String,
      default() {
        return "Input Label";
      }
    },
    type: {
      type: String,
      default() {
        return "text";
      }
    },
    required: String,
    error: Array,
    maxlength: {
      type: Number | String,
      default() {
        return 255;
      }
    },
    value: String | Number
  }
};
</script>
<style scoped>
#custom-input {
  transition: background-color 0.3s ease-out;
  transition: border-color 0.2s ease-in;
}
.this-input-label {
  transition: all 0.2s ease;
}

/* Enter and leave animations can use different */
/* durations and timing functions.              */
.fade-left-enter-active {
  transition: all 0.3s ease-out;
}
.fade-left-leave-active {
  transition: all 0.2s ease-in;
}
.fade-left-enter, .fade-left-leave-to
/* .slide-fade-leave-active below version 2.1.8 */ {
  transform: translateX(20px);
  opacity: 0;
}

/* Enter and leave animations can use different */
/* durations and timing functions.              */
.fade-right-enter-active {
  transition: all 0.3s ease-out;
}
.fade-right-leave-active {
  transition: all 0.2s ease-in;
}
.fade-right-enter, .fade-right-leave-to
/* .slide-fade-leave-active below version 2.1.8 */ {
  transform: translateX(-20px);
  opacity: 0;
}
</style>
