<template>
  <div class="relative flex flex-col gap-y-8 text-white">
    <div
      class="flex justify-between items-center h-20 bg-unselected rounded shadow"
    >
      <div class="ml-8 text-lg">1 ⋰⋅⋰ ≈ ${{ ticker.toFixed(3) }}</div>
      <button @click="clearCart" class="w-20">
        <Icon name="clear" />
      </button>
    </div>

    <transition name="pop">
      <div
        v-if="paymentAccepted"
        class="absolute w-full h-full flex items-center justify-center"
      >
        <div class="w-56">
          <LottieAnimation
            path="check_animation.json"
            :speed="2"
            :loop="false"
          />
        </div>
      </div>
      <div
        v-if="paymentRejected"
        class="absolute w-full h-full flex items-center justify-center"
      >
        <div class="w-56">
          <LottieAnimation
            path="error_animation.json"
            :speed="1"
            :loop="false"
          />
        </div>
      </div>
    </transition>

    <ul
      class="flex-1 flex flex-col gap-8 w-full overflow-y-scroll scrollbar-hidden"
    >
      <li
        v-for="item in items"
        :key="generateItemUID(item)"
      >
        <CartItem
          :item="item"
          @deleteOne="deleteOne(item)"
          @deleteAll="deleteAll(item)"
        />
      </li>
    </ul>
    <div class="flex justify-between">
      <div>Total</div>
      <div>${{ totalAmount.toFixed(2) }}</div>
    </div>
    <Button v-if="items.length" @click.native="checkout" class="h-20 text-xl">
      Checkout {{ totalNanoAmount.toFixed(3) }} ⋰⋅⋰
    </Button>
  </div>
</template>

<script lang="ts">
import { cartModule } from "@/store/cart";
import { Component, Vue } from "vue-property-decorator";
import { Item } from "@/models/item";
import { tickerModule } from "@/store/ticker";
import { paymentModule } from "@/store/payment";
import { walletModule } from "@/store/wallet";
import { tools } from "nanocurrency-web";
import LottieAnimation from "lottie-vuejs/src/LottieAnimation.vue";
import CartItem from "@/components/CartItem.vue";
import Icon from "@/components/Icon.vue";
import Button from "@/components/Button.vue";
import SHA1 from "crypto-js/sha1";

@Component({
  components: {
    LottieAnimation,
    CartItem,
    Icon,
    Button
  },
})
export default class Cart extends Vue {
  checkout() {
    cartModule.checkout();
    paymentModule.subscribeWebsocket({
      account: walletModule.address,
      price: tools.convert(this.totalNanoAmount.toFixed(3), "NANO", "RAW"),
    });
  }

  deleteOne(item: Item) {
    cartModule.removeFromCart(item);
  }

  deleteAll(item: Item) {
    cartModule.removeAllFromCart(item);
  }

  clearCart() {
    cartModule.clearCart();
  }

  generateItemUID(item: Item): string {
    const uid = item._id + item.extras.map((e) => e._id).join('-');
    return SHA1(uid).toString();
  }

  get paymentAccepted(): boolean {
    return paymentModule.paymentAccepted;
  }

  get paymentRejected(): boolean {
    return paymentModule.paymentRejected;
  }

  get ticker(): number {
    return tickerModule.price;
  }

  get items() {
    return cartModule.items;
  }

  get totalAmount() {
    return cartModule.totalAmount;
  }

  get totalNanoAmount() {
    return cartModule.totalNanoAmount;
  }
}
</script>

<style scoped>
.pop-enter-active,
.pop-leave-active {
  transition: all 200ms ease-in-out;
}

.pop-enter,
.pop-leave-to {
  transform: scale(0);
  opacity: 0.5;
}
</style>