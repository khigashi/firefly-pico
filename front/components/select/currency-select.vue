<template>
  <app-select
    :label="props.label ?? $t('currency')"
    :popupTitle="$t('currency_select.title')"
    v-model="modelValue"
    v-model:showDropdown="showDropdown"
    v-model:search="search"
    :list="filteredList"
    :columns="3"
    :getDisplayValue="getDisplayValue"
    v-bind="dynamicAttrs"
  >

    <template #label>
      <div>
        <div>{{ props.label ?? $t('currency') }}</div>
        <div v-if="info" class="text-muted text-size-11 line-height-normal">{{ info }}</div>
      </div>
    </template>

    <template #left-icon>
      <app-icon :icon="TablerIconConstants.currency" :size="20" />
    </template>

    <template #top-right>
      <van-button size="small" @click="onRefresh" class="">
        <app-icon :icon="TablerIconConstants.refresh" :stroke="1.7" size="14" />
      </van-button>
    </template>

    <template #item="{ item }">
      <app-select-option :text="Currency.getDisplayName(item)" :icon="TablerIconConstants.account" />
    </template>
  </app-select>
</template>

<script setup>
import _, { get } from 'lodash'
import { useDataStore } from '~/stores/dataStore'
import { useFormAttributes } from '~/composables/useFormAttributes'
import { IconRefresh } from '@tabler/icons-vue'

import Currency from '~/models/Currency'
import TablerIconConstants from '~/constants/TablerIconConstants'

const dataStore = useDataStore()
const attrs = useAttrs()
const { dynamicAttrs } = useFormAttributes(attrs)

const props = defineProps({
  label: {
    type: String,
  },
  info: {
    type: String,
  }
})

const modelValue = defineModel()
const showDropdown = ref(false)
const search = ref('')


let list = ref([])

const filteredList = computed(() => {
  if (search.value.length === 0) {
    return list.value
  }
  return list.value.filter((item) => {
    return Currency.getDisplayName(item).toUpperCase().indexOf(search.value.toUpperCase()) !== -1
  })
})

// ------ Methods ------

onMounted(async () => {
  list.value = dataStore.currenciesList.filter((item) => get(item, 'attributes.enabled'))
})

const getDisplayValue = (value) => {
  return Currency.getDisplayName(value)
}

const isLoading = ref(false)
UIUtils.showLoadingWhen(isLoading)
const onRefresh = async () => {
  isLoading.value = true
  await dataStore.fetchCurrencies()
  isLoading.value = false
}
</script>

<style></style>
