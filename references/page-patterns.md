# Common Page Patterns

Collection of reusable page patterns for uni-app development.

## Table of Contents

- [List Page](#list-page)
- [Detail Page](#detail-page)
- [Form Page](#form-page)
- [Search/Filter Page](#searchfilter-page)
- [Empty State Page](#empty-state-page)

## List Page

Displays a list of items with loading state, pull-to-refresh, and infinite scroll.

**Use Cases:** Parcel list, order list, user list, etc.

```vue
<template>
  <view class="list-page">
    <!-- Search bar (optional) -->
    <view class="search-bar">
      <u-search
        v-model="searchText"
        placeholder="Search..."
        :show-action="false"
        @search="handleSearch"
      ></u-search>
    </view>

    <!-- Filter tabs (optional) -->
    <u-tabs
      :list="statusTabs"
      @click="handleTabClick"
      :current="currentTab"
      line-color="#1890FF"
    ></u-tabs>

    <!-- List -->
    <view class="list-container">
      <view
        class="list-item"
        v-for="item in filteredItems"
        :key="item.id"
        @click="viewDetail(item)"
      >
        <view class="item-header">
          <text class="item-title">{{ item.title }}</text>
          <u-tag :text="getStatusText(item.status)" :type="getStatusType(item.status)"></u-tag>
        </view>

        <view class="item-body">
          <view class="info-row">
            <u-icon name="clock" color="#999" size="24"></u-icon>
            <text class="info-text">{{ item.createdAt }}</text>
          </view>
          <view class="info-row">
            <u-icon name="tag" color="#999" size="24"></u-icon>
            <text class="info-text">{{ item.category }}</text>
          </view>
        </view>
      </view>
    </view>

    <!-- Loading -->
    <view class="loading" v-if="loading">
      <u-loading mode="circle"></u-loading>
    </view>

    <!-- Empty state -->
    <u-empty v-if="!loading && filteredItems.length === 0" text="No data" mode="list"></u-empty>
  </view>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from "vue";

interface Item {
  id: number;
  title: string;
  status: number;
  createdAt: string;
  category: string;
}

const searchText = ref("");
const currentTab = ref(0);
const loading = ref(false);
const items = ref<Item[]>([]);

const statusTabs = [
  { name: "All" },
  { name: "Pending" },
  { name: "Completed" },
];

const filteredItems = computed(() => {
  let list = items.value;

  // Filter by tab
  if (currentTab.value > 0) {
    list = list.filter((item) => item.status === currentTab.value - 1);
  }

  // Filter by search
  if (searchText.value) {
    list = list.filter((item) =>
      item.title.toLowerCase().includes(searchText.value.toLowerCase())
    );
  }

  return list;
});

onMounted(() => {
  loadData();
});

const loadData = async () => {
  loading.value = true;
  try {
    // TODO: Replace with actual API call
    // const data = await api.getItems();
    // items.value = data;
    items.value = [];
  } finally {
    loading.value = false;
  }
};

const handleSearch = () => {
  // Search logic handled by computed
};

const handleTabClick = (index: number) => {
  currentTab.value = index;
};

const viewDetail = (item: Item) => {
  uni.navigateTo({ url: `/pages/detail/index?id=${item.id}` });
};

const getStatusText = (status: number) => {
  const map = { 0: "Pending", 1: "Completed" };
  return map[status] || "Unknown";
};

const getStatusType = (status: number) => {
  const map = { 0: "warning", 1: "success" };
  return map[status] || "default";
};
</script>

<style lang="scss" scoped>
.list-page {
  min-height: 100vh;
  background-color: $color-bg-page;
}

.search-bar {
  padding: $spacing-md;
  background-color: $color-bg-card;
}

.list-container {
  padding: $spacing-md;

  .list-item {
    background-color: $color-bg-card;
    border-radius: $border-radius-md;
    padding: $spacing-md;
    margin-bottom: $spacing-md;

    .item-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: $spacing-sm;

      .item-title {
        font-size: $font-size-base;
        font-weight: bold;
        color: $color-text-primary;
      }
    }

    .item-body {
      .info-row {
        display: flex;
        align-items: center;
        gap: $spacing-sm;
        margin-bottom: $spacing-xs;

        &:last-child {
          margin-bottom: 0;
        }

        .info-text {
          font-size: $font-size-sm;
          color: $color-text-secondary;
        }
      }
    }
  }
}

.loading {
  display: flex;
  justify-content: center;
  padding: $spacing-lg;
}
</style>
```

## Detail Page

Displays detailed information about a single item.

**Use Cases:** Parcel detail, order detail, user profile, etc.

```vue
<template>
  <view class="detail-page">
    <!-- Header card -->
    <view class="header-card">
      <view class="header-row">
        <text class="title">{{ detail.title }}</text>
        <u-tag :text="getStatusText(detail.status)" :type="getStatusType(detail.status)"></u-tag>
      </view>
      <view class="subtitle">{{ detail.subtitle }}</view>
    </view>

    <!-- Info sections -->
    <view class="info-sections">
      <!-- Section 1 -->
      <u-card :border="false" padding="20" margin="20">
        <template #title>
          <view class="section-title">
            <u-icon name="info-circle" color="#1890FF" size="32"></u-icon>
            <text>Basic Info</text>
          </view>
        </template>
        <view class="info-list">
          <view class="info-item">
            <text class="label">ID:</text>
            <text class="value">{{ detail.id }}</text>
          </view>
          <view class="info-item">
            <text class="label">Created:</text>
            <text class="value">{{ detail.createdAt }}</text>
          </view>
        </view>
      </u-card>

      <!-- Section 2 -->
      <u-card :border="false" padding="20" margin="20">
        <template #title>
          <view class="section-title">
            <u-icon name="list" color="#1890FF" size="32"></u-icon>
            <text>Details</text>
          </view>
        </template>
        <view class="detail-content">
          {{ detail.description }}
        </view>
      </u-card>
    </view>

    <!-- Bottom actions -->
    <view class="bottom-actions">
      <u-button type="primary" @click="handleAction">Action</u-button>
    </view>
  </view>
</template>

<script setup lang="ts">
import { ref, onMounted } from "vue";
import { onLoad } from "@dcloudio/uni-app";

interface Detail {
  id: number;
  title: string;
  subtitle: string;
  status: number;
  createdAt: string;
  description: string;
}

const detail = ref<Detail>({
  id: 0,
  title: "",
  subtitle: "",
  status: 0,
  createdAt: "",
  description: "",
});

onLoad((options: any) => {
  const id = options.id;
  if (id) {
    loadDetail(id);
  }
});

const loadDetail = async (id: number) => {
  // TODO: Replace with actual API call
  // const data = await api.getDetail(id);
  // detail.value = data;
};

const getStatusText = (status: number) => {
  const map = { 0: "Pending", 1: "Completed" };
  return map[status] || "Unknown";
};

const getStatusType = (status: number) => {
  const map = { 0: "warning", 1: "success" };
  return map[status] || "default";
};

const handleAction = () => {
  uni.showToast({ title: "Action triggered", icon: "success" });
};
</script>

<style lang="scss" scoped>
.detail-page {
  min-height: 100vh;
  background-color: $color-bg-page;
  padding-bottom: 120rpx;
}

.header-card {
  background-color: $color-bg-card;
  padding: $spacing-md;
  margin-bottom: $spacing-md;

  .header-row {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: $spacing-sm;

    .title {
      font-size: $font-size-xl;
      font-weight: bold;
      color: $color-text-primary;
    }
  }

  .subtitle {
    font-size: $font-size-sm;
    color: $color-text-secondary;
  }
}

.info-sections {
  .section-title {
    display: flex;
    align-items: center;
    gap: $spacing-sm;

    text {
      font-size: $font-size-lg;
      font-weight: bold;
      color: $color-text-primary;
    }
  }

  .info-list {
    .info-item {
      display: flex;
      justify-content: space-between;
      margin-bottom: $spacing-sm;

      &:last-child {
        margin-bottom: 0;
      }

      .label {
        font-size: $font-size-base;
        color: $color-text-secondary;
      }

      .value {
        font-size: $font-size-base;
        color: $color-text-primary;
      }
    }
  }

  .detail-content {
    font-size: $font-size-base;
    color: $color-text-primary;
    line-height: 1.6;
  }
}

.bottom-actions {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  background-color: $color-bg-card;
  padding: $spacing-md;
  box-shadow: 0 -2rpx 8rpx rgba(0, 0, 0, 0.1);
}
</style>
```

## Form Page

Form for creating or editing data.

**Use Cases:** Create parcel, edit profile, etc.

```vue
<template>
  <view class="form-page">
    <u-form :model="form" ref="formRef" :rules="rules">
      <!-- Input field -->
      <u-form-item label="Title" prop="title" required>
        <u-input
          v-model="form.title"
          placeholder="Please enter title"
          border="surround"
        ></u-input>
      </u-form-item>

      <!-- Textarea -->
      <u-form-item label="Description" prop="description">
        <u-textarea
          v-model="form.description"
          placeholder="Please enter description"
          border="surround"
        ></u-textarea>
      </u-form-item>

      <!-- Select -->
      <u-form-item label="Category" prop="category">
        <u-picker
          :show="showCategoryPicker"
          :columns="categoryOptions"
          @confirm="confirmCategory"
          @cancel="showCategoryPicker = false"
        ></u-picker>
        <u-input
          v-model="form.category"
          placeholder="Select category"
          readonly
          @click="showCategoryPicker = true"
        ></u-input>
      </u-form-item>

      <!-- Date picker -->
      <u-form-item label="Date" prop="date">
        <u-datetime-picker
          :show="showDatePicker"
          v-model="form.date"
          mode="date"
          @confirm="showDatePicker = false"
          @cancel="showDatePicker = false"
        ></u-datetime-picker>
        <u-input
          v-model="form.date"
          placeholder="Select date"
          readonly
          @click="showDatePicker = true"
        ></u-input>
      </u-form-item>

      <!-- Checkbox -->
      <u-form-item label="Options">
        <u-checkbox-group v-model="form.options">
          <u-checkbox
            v-for="opt in optionList"
            :key="opt.value"
            :name="opt.value"
            :label="opt.label"
          ></u-checkbox>
        </u-checkbox-group>
      </u-form-item>
    </u-form>

    <!-- Submit button -->
    <view class="submit-section">
      <u-button type="primary" @click="handleSubmit" :loading="submitting">
        Submit
      </u-button>
    </view>
  </view>
</template>

<script setup lang="ts">
import { ref, reactive } from "vue";

const formRef = ref();
const submitting = ref(false);
const showCategoryPicker = ref(false);
const showDatePicker = ref(false);

const form = reactive({
  title: "",
  description: "",
  category: "",
  date: "",
  options: [],
});

const rules = {
  title: [
    { required: true, message: "Please enter title", trigger: "blur" },
  ],
};

const categoryOptions = [
  { label: "Option 1", value: "1" },
  { label: "Option 2", value: "2" },
];

const optionList = [
  { label: "Option A", value: "a" },
  { label: "Option B", value: "b" },
];

const confirmCategory = (e: any) => {
  form.category = e.value[0].label;
  showCategoryPicker.value = false;
};

const handleSubmit = async () => {
  try {
    const valid = await formRef.value.validate();
    if (!valid) return;

    submitting.value = true;

    // TODO: Call API to submit form
    // await api.submitForm(form);

    uni.showToast({ title: "Success", icon: "success" });
    setTimeout(() => {
      uni.navigateBack();
    }, 1500);
  } catch (error) {
    console.error(error);
  } finally {
    submitting.value = false;
  }
};
</script>

<style lang="scss" scoped>
.form-page {
  padding: $spacing-md;
}

.submit-section {
  margin-top: $spacing-xl;
}
</style>
```

## Search/Filter Page

Advanced search and filter page.

```vue
<template>
  <view class="search-page">
    <!-- Search input -->
    <view class="search-section">
      <u-search
        v-model="searchQuery"
        placeholder="Search..."
        :show-action="true"
        action-text="Search"
        @search="handleSearch"
        @custom="handleSearch"
      ></u-search>
    </view>

    <!-- Filters -->
    <view class="filter-section">
      <view class="filter-group">
        <text class="filter-label">Status:</text>
        <u-checkbox-group v-model="filterStatus" @change="handleFilterChange">
          <u-checkbox
            v-for="item in statusOptions"
            :key="item.value"
            :name="item.value"
            :label="item.label"
            shape="circle"
          ></u-checkbox>
        </u-checkbox-group>
      </view>

      <view class="filter-group">
        <text class="filter-label">Date Range:</text>
        <u-datetime-picker
          :show="showStartDatePicker"
          v-model="filterStartDate"
          mode="date"
          @confirm="showStartDatePicker = false"
        ></u-datetime-picker>
        <u-input
          v-model="filterStartDate"
          placeholder="Start date"
          readonly
          @click="showStartDatePicker = true"
        ></u-input>
      </view>
    </view>

    <!-- Results -->
    <view class="results-section">
      <text class="results-count">{{ results.length }} results</text>
      <!-- Reuse list component -->
    </view>
  </view>
</template>

<script setup lang="ts">
import { ref } from "vue";

const searchQuery = ref("");
const filterStatus = ref([]);
const filterStartDate = ref("");
const showStartDatePicker = ref(false);
const results = ref([]);

const statusOptions = [
  { label: "Pending", value: 0 },
  { label: "Completed", value: 1 },
];

const handleSearch = () => {
  // Perform search
};

const handleFilterChange = () => {
  // Update results based on filters
};
</script>

<style lang="scss" scoped>
.search-page {
  padding: $spacing-md;
}

.search-section {
  margin-bottom: $spacing-md;
}

.filter-section {
  .filter-group {
    margin-bottom: $spacing-md;

    .filter-label {
      display: block;
      font-size: $font-size-base;
      color: $color-text-primary;
      margin-bottom: $spacing-sm;
    }
  }
}

.results-section {
  margin-top: $spacing-md;

  .results-count {
    font-size: $font-size-sm;
    color: $color-text-secondary;
  }
}
</style>
```

## Empty State Page

Placeholder page for features under development.

```vue
<template>
  <view class="empty-page">
    <u-empty
      text="Feature under development"
      mode="data"
      icon="filetext"
    >
      <template #extra>
        <u-button type="primary" size="small" @click="goBack">
          Go Back
        </u-button>
      </template>
    </u-empty>
  </view>
</template>

<script setup lang="ts">
const goBack = () => {
  uni.navigateBack();
};
</script>

<style lang="scss" scoped>
.empty-page {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
```

## Best Practices

1. **Always use TypeScript interfaces** for data structures
2. **Handle loading states** with visual feedback
3. **Provide empty states** when no data is available
4. **Use computed properties** for filtering and searching
5. **Debounce search input** to avoid excessive API calls
6. **Validate forms** before submission
7. **Show success/error feedback** after operations
8. **Implement error handling** with try-catch blocks
9. **Use proper navigation patterns** (navigateTo, redirectTo, switchTab)
10. **Follow consistent styling** with theme variables
