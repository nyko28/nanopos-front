<template>
  <div
    class="absolute z-50 top-0 left-0 h-full w-full bg-black bg-opacity-50 flex items-center justify-center"
  >
    <div class="shadow">
      <div
        class="flex gap-x-6 items-center rounded-t bg-lighter p-8 text-darker"
      >
        <Icon name="lock" :outlined="true" class="text-4xl" />
        <div class="text-xl">
          Your session has been locked due to inactivity
        </div>
      </div>

      <Button :full="true" @click.native="onClick">
        Unlock
      </Button>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import Icon from "@/components/Icon.vue";
import Button from "@/components/Button.vue";

@Component({
  components: {
    Icon,
    Button
  },
})
export default class InactiveModal extends Vue {
  onClick() {
    this.$emit("click");
  }
}
</script>