<template>
  <z-modal
    :visible="visible"
    custom-class="overflow-visible"
    @close="$emit('close')">
    <div 
      style="min-width:450px" 
      class="mx-4">
      <div class="font-semibold text-2xl mb-6 text-gray-800">
        Stake <b>ZIL</b>
      </div>
      <div class="flex flex-col w-full">
        <div class="flex flex-row items-center justify-between">
          <div class="tracking-wide text-sm font-semibold mb-2">
            Amount
          </div>
          <div
            class=" text-sm cursor-pointer"
            @click="amount=(Balance.zil && Number(Balance.zil).toFixed(4) - 20)"
          >
            <b class="text-teal-600">{{ Balance.zil && Number(Balance.zil).toFixed(4) - 20 }}</b>  Availble 
          </div>
        </div>
        <div class="flex flex-row">
          <z-input
            v-model.number="amount"
            :hide="false"
            :valid="validateCryptoAmount"
            class="mb-0"
            number
            placeholder="0 ZIL" />
          <z-button
            type="default"
            style="height:3rem; left:-3px"
            class="m-0 relative bg-white border-gray-400 rounded-r"
            @click="amount=(Balance.zil && Number(Balance.zil).toFixed(4) - 20)">
            Max
          </z-button>
        </div>
      </div>
      <div class="flex flex-row items-center justify-between">
        <div class="tracking-wide text-sm font-semibold mb-2">
          Delegting to
        </div>
      </div>
      <div class="relative">
        <div
          v-if="seedNodeDropDown"
          class="fixed inset-0"
          @click="seedNodeDropDown = false" />
        <span
          class="flex flex-row items-center cursor-pointer justify-between focus:outline-none bg-white text-gray-900 
                  h-10 px-4 py-2 rounded border border-gray-200 w-full"
          @click="seedNodeDropDown=true">
          <div class="flex flex-column items-center justify-between w-full">
            <p class="text-gray-800 font-bold pl-2">
              {{ selectedSeedNode.name }}
            </p>
            <p class="text-gray-800 pr-2">
              {{ selectedSeedNode.address }}
            </p>
          </div>
          <span class="absolute inset-y-0 right-0 flex items-center pr-2 pointer-events-none">
            <svg
              class="h-5 w-5 text-gray-600"
              viewBox="0 0 20 20"
              fill="none"
              stroke="currentColor">
              <path
                d="M7 7l3-3 3 3m0 6l-3 3-3-3"
                stroke-width="1.5"
                stroke-linecap="round"
                stroke-linejoin="round" />
            </svg>
          </span>
        </span>
        <transition
          enter-active-class="transition-all transition-fastest ease-out-quad"
          leave-active-class="transition-all transition-faster ease-in-quad"
          enter-class="opacity-0 scale-70"
          enter-to-class="opacity-100 scale-100"
          leave-class="opacity-100 scale-100"
          leave-to-class="opacity-0 scale-70">
          <div
            v-if="seedNodeDropDown"
            class="origin-top-right absolute left-0 bg-white rounded border shadow-md w-full z-50 "
            style="max-height:250px; overflow-y:scroll"
          >
            <div
              v-for="n in orderBy(formattedList,'name')"
              :key="n.address"
              :class="{'bg-gray-200':selectedSeedNode.address === n.address}"
              class="flex flex-column items-center justify-between text-left px-4 py-3 
              text-sm cursor-pointer hover:bg-grey-lightest"
              @click="fromTokenChange(n, false)">
              <p class="text-gray-800 font-bold pl-2">
                {{ n.name }}
              </p>
              <p class="text-gray-800 pr-2">
                {{ n.address }}
              </p>
            </div>
          </div>
        </transition>
      </div>
      <p class="italic text-left text-sm mt-4">
        * Select a node in order to delegate to someone else.
      </p>
      <div class="flex flex-row items-center justify-between">
        <z-button
          class="rounded-full py-2 mr-2 w-40"
          type="default"
          :disabled="loading"
          size="medium"
          @click="$emit('close')">
          Cancel
        </z-button>
        <z-button
          size="medium"
          :loading="loading"
          class="rounded-full py-2 shadow-md ml-2 w-full"
          @click="$emit('stake', amount, selectedSeedNode.address)">
          Stake
        </z-button>
      </div>
    </div>
  </z-modal>
</template>

<script>
import { mapState, mapGetters } from 'vuex';
import Vue2Filters from 'vue2-filters';
import { isNumber } from '@/utils/validation';

export default {
  name: 'AddToken',
  mixins: [Vue2Filters.mixin],
  props: {
    visible: {
      default: false,
      type: Boolean
    },
    ssnlist: {
      required: true,
      type: [Array, Object]
    },
    loading: {
      default: false,
      type: Boolean
    }
  },
  data() {
    return {
      fetchingDetails: false,
      amount: 0,
      seedNodeDropDown: false,
      selectedSeedNode: {}
    };
  },
  computed: {
    ...mapGetters(['Balance']),
    formattedList() {
      let list = [];
      for (const key in this.ssnlist) {
        list.push({
          address: key,
          name: this.ssnlist[key].arguments[3]
        });
      }
      return list;
    },
    validateCryptoAmount() {
      if (!isNumber(this.amount)) {
        return false;
      }
      return parseFloat(this.amount) < this.Balance.zil - 50;
    }
  },
  watch: {
    formattedList: {
      handler(newValue, oldValue) {
        let obj = newValue.find(o => o.name.toLowerCase() === 'zillet');
        if (obj) {
          this.selectedSeedNode = obj;
        }
      }
    }
  },
  mounted() {},
  methods: {
    fromTokenChange(node) {
      this.selectedSeedNode = node;
      this.seedNodeDropDown = false;
    },
    stake() {}
  }
};
</script>

<style>
</style>