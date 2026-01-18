<template>
  <view class="page-container">
    <!-- Page header with title -->
    <view class="page-header">
      <text class="page-title">Page Title</text>
      <text class="page-subtitle">Optional subtitle</text>
    </view>

    <!-- Content area -->
    <view class="page-content">
      <!-- Add your page content here -->
    </view>

    <!-- Loading state -->
    <view class="loading-container" v-if="loading">
      <u-loading mode="circle"></u-loading>
    </view>

    <!-- Empty state (shown when no data) -->
    <u-empty
      v-if="!loading && items.length === 0"
      text="No data available"
      mode="data"
    >
      <template #extra>
        <u-button type="primary" size="small" @click="onRefresh">
          Refresh
        </u-button>
      </template>
    </u-empty>
  </view>
</template>

<script setup lang="ts">
import { ref, onMounted } from "vue";
import { onLoad } from "@dcloudio/uni-app";

// Define data interfaces
interface Item {
  id: number;
  title: string;
  description?: string;
  [key: string]: any;
}

// Reactive state
const loading = ref(false);
const items = ref<Item[]>([]);
const error = ref<string | null>(null);

// Props (if component receives props)
interface Props {
  id?: string | number;
}

const props = defineProps<Props>();

// Page lifecycle
onLoad((options: any) => {
  console.log("Page loaded with options:", options);
  loadData();
});

onMounted(() => {
  // Additional setup after component mount
});

// Functions
const loadData = async () => {
  loading.value = true;
  error.value = null;

  try {
    // TODO: Replace with actual API call
    // const data = await api.getItems(props.id);
    // items.value = data;

    // Simulate API call
    await new Promise(resolve => setTimeout(resolve, 1000));
    items.value = [];
  } catch (err) {
    error.value = err instanceof Error ? err.message : "Failed to load data";
    uni.showToast({
      title: error.value,
      icon: "none",
    });
  } finally {
    loading.value = false;
  }
};

const onItemClick = (item: Item) => {
  uni.navigateTo({
    url: `/pages/detail/index?id=${item.id}`,
  });
};

const onRefresh = () => {
  loadData();
};
</script>

<style lang="scss" scoped>
.page-container {
  min-height: 100vh;
  background-color: $color-bg-page;
}

.page-header {
  background-color: $color-bg-card;
  padding: $spacing-md;
  margin-bottom: $spacing-md;

  .page-title {
    display: block;
    font-size: $font-size-xl;
    font-weight: bold;
    color: $color-text-primary;
    margin-bottom: $spacing-xs;
  }

  .page-subtitle {
    display: block;
    font-size: $font-size-sm;
    color: $color-text-secondary;
  }
}

.page-content {
  padding: $spacing-md;
}

.loading-container {
  display: flex;
  justify-content: center;
  align-items: center;
  padding: $spacing-xl;
}
</style>
