<template>
  <transition-group class="flex items-center min-h-16 gap-x-8" name="slide-fade">
    <div
      @click="onItemClick"
      key="details"
      class="flex-grow transition-all duration-200"
    >
      <div class="flex font-medium text-xl">
        <div class="w-14">x{{ item.count }}</div>
        <div class="flex-1 flex justify-between">
          <div>{{ item.name }}</div>
          <div>${{ item.price.toFixed(2) }}</div>
        </div>
      </div>
      <ul v-if="item.extras.length" class="font-light text-lg text-gray-400">
        <li
          v-for="extra in item.extras"
          :key="extra._id"
          class="ml-14 flex justify-between"
        >
          <div>{{ extra.name }}</div>
          <div>+ ${{ extra.price.toFixed(2) }}</div>
        </li>
      </ul>
    </div>
    <div v-if="revealButtons" key="buttons" class="flex w-40 h-16 shadow">
      <button
        @click="onDeleteAllClick"
        :class="[
          'flex-1 flex items-center justify-center bg-cancel',
          'active:bg-opacity-80 transition-all duration-200',
          item.count <= 1 ? 'rounded' : 'rounded-l',
        ]"
      >
        <Icon name="delete" />
      </button>
      <button
        @click="onDeleteOneClick"
        v-if="item.count > 1"
        :class="[
          'flex-1 flex items-center justify-center bg-indigo-600 rounded-r',
          'active:bg-opacity-80 transition-all duration-200',
        ]"
      >
        <Icon name="remove" />
      </button>
    </div>
  </transition-group>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import Icon from "@/components/Icon.vue";
import { Item } from "@/models/item";

@Component({
  components: {
    Icon,
  },
})
export default class CartItem extends Vue {
  @Prop()
  item!: Item;

  revealButtons = false;

  onItemClick(): void {
    this.revealButtons = !this.revealButtons;
  }

  onDeleteAllClick(): void {
    this.$emit("deleteAll");
  }

  onDeleteOneClick(): void {
    this.$emit("deleteOne");
  }
}
</script>

<style scoped>
.min-h-16 {
  min-height: 4rem;
}

.slide-fade-enter-active {
  transition: all 200ms ease;
}
.slide-fade-leave-active {
  transition: all 200ms ease;
}
.slide-fade-enter,
.slide-fade-leave-to {
  transform: translateX(10px);
  opacity: 0;
}
.slide-fade-move {
  transition: transform 200ms;
}
</style>