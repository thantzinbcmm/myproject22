<script setup>
import { ref, computed } from 'vue'
import { Head, Link, router } from '@inertiajs/vue3'
import AppLayout from '@/Layouts/AppLayout.vue'

const props = defineProps({
    orders: Object,  // paginated
    filters: Object,
})

const search   = ref(props.filters?.search ?? '')
const status   = ref(props.filters?.status ?? '')

const statusColors = {
    pending:    'bg-yellow-100 text-yellow-800',
    processing: 'bg-blue-100   text-blue-800',
    shipped:    'bg-purple-100 text-purple-800',
    completed:  'bg-green-100  text-green-800',
    cancelled:  'bg-red-100    text-red-800',
}

function applyFilters() {
    router.get(route('orders.index'), { search: search.value, status: status.value }, {
        preserveScroll: true,
        preserveState: true,
    })
}
</script>

<template>
    <Head title="Orders" />
    <AppLayout>
        <div class="p-6 space-y-4">
            <!-- Filters -->
            <div class="flex gap-3">
                <input v-model="search" @keyup.enter="applyFilters"
                       placeholder="Search customer..." class="input" />
                <select v-model="status" @change="applyFilters" class="input w-40">
                    <option value="">All Status</option>
                    <option value="pending">Pending</option>
                    <option value="processing">Processing</option>
                    <option value="completed">Completed</option>
                </select>
                <Link :href="route('orders.create')" class="btn-primary">+ New Order</Link>
            </div>

            <!-- Table -->
            <table class="w-full text-sm">
                <thead>
                    <tr class="border-b">
                        <th class="text-left py-2">Order #</th>
                        <th class="text-left py-2">Customer</th>
                        <th class="text-left py-2">Status</th>
                        <th class="text-right py-2">Total</th>
                        <th class="text-left py-2">Date</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="order in orders.data" :key="order.id"
                        class="border-b hover:bg-gray-50 cursor-pointer"
                        @click="router.visit(route('orders.show', order.id))">
                        <td class="py-2 font-mono text-xs">{{ order.order_number }}</td>
                        <td class="py-2">{{ order.customer_name }}</td>
                        <td class="py-2">
                            <span class="px-2 py-0.5 rounded-full text-xs font-medium"
                                  :class="statusColors[order.status]">
                                {{ order.status }}
                            </span>
                        </td>
                        <td class="py-2 text-right font-mono">
                            ¥{{ Number(order.total_amount).toLocaleString() }}
                        </td>
                        <td class="py-2 text-xs text-gray-500">
                            {{ new Date(order.created_at).toLocaleDateString() }}
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>
    </AppLayout>
</template>