<template>
  <van-cell-group inset>
    <div class="van-cell-group-title">{{ $t('budgets') }}:</div>

    <template v-if="hasBudgets">
      <div class="flex-center-vertical justify-content-center flex-wrap gap-2 m-2">
        <app-chip :title="`${$t('dashboard.budget.budgeted')}:`" :subtitle="budgetLimitTotalFormatted" />
        <app-chip :title="`${$t('dashboard.budget.spent')}:`" :subtitle="budgetLimitSpentFormatted" />
        <app-chip :title="`${$t('dashboard.budget.remaining')}:`" :subtitle="budgetLimitRemainingFormatted" />
      </div>

      <van-grid :column-num="3">
        <dashboard-budget-item v-for="budget in budgetList" :value="budget" />
      </van-grid>
    </template>

    <div v-else class="text-muted text-size-12 px-3 mb-15" style="margin-top: -10px">No budgets ^_^</div>
  </van-cell-group>
</template>

<script setup>
import DashboardBudgetItem from '~/components/dashboard/dashboard-budgets/dashboard-budget-item.vue'
import { get } from 'lodash'
import Transaction from '~/models/Transaction.js'
import TablerIconConstants from '~/constants/TablerIconConstants.js'
import AppChip from '~/components/ui-kit/app-chip.vue'
import Budget from '~/models/Budget.js'

const dataStore = useDataStore()
// const { t } = useI18n()

const budgetList = dataStore.budgetList.filter(item => Budget.isActive(item))
const hasBudgets = computed(() => budgetList.length > 0)

const budgetLimitTotalFormatted = computed(() => `${formatNumberForDashboard(dataStore.budgetLimitTotal)} ${dataStore.dashboardCurrencyCode}`)
const budgetLimitSpentFormatted = computed(() => `${formatNumberForDashboard(dataStore.budgetLimitSpent)} ${dataStore.dashboardCurrencyCode}`)
const budgetLimitRemainingFormatted = computed(() => `${formatNumberForDashboard(dataStore.budgetLimitRemaining) } ${dataStore.dashboardCurrencyCode}`)
</script>
