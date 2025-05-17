<template>
    <div id="app" v-cloak>
        <nav class="bg-indigo-700 text-white p-4">
            <div class="container mx-auto flex justify-between items-center">
                <div class="text-xl font-bold">Admin Dashboard</div>
                <div class="flex space-x-4">
                    <button
                        @click="activeTab = 'revenue'"
                        :class="{ 'active-tab': activeTab === 'revenue' }"
                        class="px-3 py-2"
                    >
                        Revenue Analysis
                    </button>
                    <button
                        @click="activeTab = 'inventory'"
                        :class="{ 'active-tab': activeTab === 'inventory' }"
                        class="px-3 py-2"
                    >
                        Inventory Management
                    </button>
                    <button
                        @click="activeTab = 'product'"
                        :class="{ 'active-tab': activeTab === 'product' }"
                        class="px-3 py-2"
                    >
                        Product Registration
                    </button>
                </div>
            </div>
        </nav>

        <div class="container mx-auto p-4">
            <div v-if="activeTab === 'revenue'">
                <h1 class="text-2xl font-bold mb-4">Revenue Analysis</h1>
                <div class="grid grid-cols-1 md:grid-cols-4 gap-4 mb-6">
                    <div class="bg-white rounded-lg shadow p-4 card">
                        <h3 class="text-gray-500">Total Orders</h3>
                        <p class="text-2xl font-bold">{{ totalOrders }}</p>
                        <p class="text-sm text-green-500">
                            +{{ orderGrowth }}% from last period
                        </p>
                    </div>
                    <div class="bg-white rounded-lg shadow p-4 card">
                        <h3 class="text-gray-500">Total Revenue</h3>
                        <p class="text-2xl font-bold">
                            ${{ totalRevenue.toLocaleString() }}
                        </p>
                        <p class="text-sm text-green-500">
                            +{{ revenueGrowth }}% from last period
                        </p>
                    </div>
                    <div class="bg-white rounded-lg shadow p-4 card">
                        <h3 class="text-gray-500">Avg. Order Value</h3>
                        <p class="text-2xl font-bold">${{ averageOrderValue }}</p>
                        <p class="text-sm text-green-500">
                            +{{ aovGrowth }}% from last period
                        </p>
                    </div>
                    <div class="bg-white rounded-lg shadow p-4 card">
                        <h3 class="text-gray-500">Conversion Rate</h3>
                        <p class="text-2xl font-bold">{{ conversionRate }}%</p>
                        <p class="text-sm text-red-500">
                            -{{ conversionDrop }}% from last period
                        </p>
                    </div>
                </div>
                <div class="bg-white rounded-lg shadow p-4 mb-6">
                    <div class="flex flex-wrap items-center gap-4">
                        <div>
                            <label class="block text-sm font-medium text-gray-700"
                                >Time Period</label
                            >
                            <select
                                v-model="timeFilter"
                                class="mt-1 block w-full py-2 px-3 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
                            >
                                <option value="daily">Daily</option>
                                <option value="weekly">Weekly</option>
                                <option value="monthly">Monthly</option>
                                <option value="yearly">Yearly</option>
                            </select>
                        </div>
                        <div>
                            <label class="block text-sm font-medium text-gray-700"
                                >Category</label
                            >
                            <select
                                v-model="categoryFilter"
                                class="mt-1 block w-full py-2 px-3 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
                            >
                                <option value="all">All Categories</option>
                                <option value="electronics">Electronics</option>
                                <option value="clothing">Clothing</option>
                                <option value="home">Home & Kitchen</option>
                                <option value="beauty">Beauty & Personal Care</option>
                            </select>
                        </div>
                        <div>
                            <label class="block text-sm font-medium text-gray-700"
                                >Platform</label
                            >
                            <select
                                v-model="platformFilter"
                                class="mt-1 block w-full py-2 px-3 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
                            >
                                <option value="all">All Platforms</option>
                                <option value="amazon">Amazon</option>
                                <option value="walmart">Walmart</option>
                            </select>
                        </div>
                        <div class="ml-auto">
                            <button
                                @click="applyFilters"
                                class="bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-2 px-4 rounded mt-5"
                            >
                                Apply Filters
                            </button>
                        </div>
                    </div>
                </div>
                <div class="grid grid-cols-1 lg:grid-cols-2 gap-6 mb-6">
                    <div class="bg-white rounded-lg shadow p-4">
                        <h3 class="text-lg font-semibold mb-4">Revenue Trends</h3>
                        <canvas id="revenueChart" height="250"></canvas>
                    </div>
                    <div class="bg-white rounded-lg shadow p-4">
                        <h3 class="text-lg font-semibold mb-4">Orders by Platform</h3>
                        <canvas id="ordersChart" height="250"></canvas>
                    </div>
                </div>
                <div class="bg-white rounded-lg shadow p-4">
                    <h3 class="text-lg font-semibold mb-4">Revenue by Category</h3>
                    <canvas id="categoryChart" height="100"></canvas>
                </div>
            </div>
            <div v-if="activeTab === 'inventory'">
                <h1 class="text-2xl font-bold mb-4">Inventory Management</h1>
                <div class="bg-white rounded-lg shadow p-4 mb-6">
                    <div class="flex flex-wrap items-center gap-4">
                        <div class="flex-grow">
                            <label class="block text-sm font-medium text-gray-700"
                                >Search Products</label
                            >
                            <input
                                type="text"
                                v-model="searchTerm"
                                placeholder="Search by name, SKU, or description"
                                class="mt-1 block w-full py-2 px-3 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
                            />
                        </div>
                        <div>
                            <label class="block text-sm font-medium text-gray-700"
                                >Category</label
                            >
                            <select
                                v-model="inventoryCategoryFilter"
                                class="mt-1 block w-full py-2 px-3 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
                            >
                                <option value="all">All Categories</option>
                                <option value="electronics">Electronics</option>
                                <option value="clothing">Clothing</option>
                                <option value="home">Home & Kitchen</option>
                                <option value="beauty">Beauty & Personal Care</option>
                            </select>
                        </div>
                        <div>
                            <label class="block text-sm font-medium text-gray-700"
                                >Stock Status</label
                            >
                            <select
                                v-model="stockFilter"
                                class="mt-1 block w-full py-2 px-3 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
                            >
                                <option value="all">All Stock</option>
                                <option value="instock">In Stock</option>
                                <option value="low">Low Stock</option>
                                <option value="outofstock">Out of Stock</option>
                            </select>
                        </div>
                        <div class="ml-auto">
                            <button
                                @click="applyInventoryFilters"
                                class="bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-2 px-4 rounded mt-5"
                            >
                                Apply Filters
                            </button>
                        </div>
                    </div>
                </div>
                <div class="grid grid-cols-1 md:grid-cols-4 gap-4 mb-6">
                    <div class="bg-white rounded-lg shadow p-4 card">
                        <h3 class="text-gray-500">Total Products</h3>
                        <p class="text-2xl font-bold">{{ totalProducts }}</p>
                    </div>
                    <div class="bg-white rounded-lg shadow p-4 card">
                        <h3 class="text-gray-500">Products In Stock</h3>
                        <p class="text-2xl font-bold">{{ inStockProducts }}</p>
                    </div>
                    <div class="bg-white rounded-lg shadow p-4 card">
                        <h3 class="text-gray-500">Low Stock Items</h3>
                        <p class="text-2xl font-bold text-yellow-500">
                            {{ lowStockProducts }}
                        </p>
                    </div>
                    <div class="bg-white rounded-lg shadow p-4 card">
                        <h3 class="text-gray-500">Out of Stock Items</h3>
                        <p class="text-2xl font-bold text-red-500">
                            {{ outOfStockProducts }}
                        </p>
                    </div>
                </div>
                <div class="bg-white rounded-lg shadow p-4 mb-6">
                    <h3 class="text-lg font-semibold mb-4">Products Inventory</h3>
                    <div
                        id="inventoryGrid"
                        class="ag-theme-alpine"
                        style="height: 500px; width: 100%"
                    ></div>
                </div>
            </div>
            <div v-if="activeTab === 'product'">
                <h1 class="text-2xl font-bold mb-4">Product Registration</h1>
                <div class="bg-white rounded-lg shadow p-6">
                    <form @submit.prevent="registerProduct">
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                            <div>
                                <label class="block text-sm font-medium text-gray-700"
                                    >Product Name</label
                                >
                                <input
                                    type="text"
                                    v-model="newProduct.name"
                                    required
                                    class="mt-1 block w-full py-2 px-3 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
                                />
                            </div>
                            <div>
                                <label class="block text-sm font-medium text-gray-700"
                                    >SKU</label
                                >
                                <input
                                    type="text"
                                    v-model="newProduct.sku"
                                    required
                                    class="mt-1 block w-full py-2 px-3 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
                                />
                            </div>
                            <div>
                                <label class="block text-sm font-medium text-gray-700"
                                    >Category</label
                                >
                                <select
                                    v-model="newProduct.category"
                                    required
                                    class="mt-1 block w-full py-2 px-3 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
                                >
                                    <option value="electronics">Electronics</option>
                                    <option value="clothing">Clothing</option>
                                    <option value="home">Home & Kitchen</option>
                                    <option value="beauty">Beauty & Personal Care</option>
                                </select>
                            </div>
                            <div>
                                <label class="block text-sm font-medium text-gray-700"
                                    >Platform</label
                                >
                                <select
                                    v-model="newProduct.platform"
                                    required
                                    class="mt-1 block w-full py-2 px-3 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
                                >
                                    <option value="amazon">Amazon</option>
                                    <option value="walmart">Walmart</option>
                                    <option value="both">Both Platforms</option>
                                </select>
                            </div>
                            <div>
                                <label class="block text-sm font-medium text-gray-700"
                                    >Price ($)</label
                                >
                                <input
                                    type="number"
                                    v-model="newProduct.price"
                                    min="0"
                                    step="0.01"
                                    required
                                    class="mt-1 block w-full py-2 px-3 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
                                />
                            </div>
                            <div>
                                <label class="block text-sm font-medium text-gray-700"
                                    >Initial Stock Level</label
                                >
                                <input
                                    type="number"
                                    v-model="newProduct.stock"
                                    min="0"
                                    required
                                    class="mt-1 block w-full py-2 px-3 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
                                />
                            </div>
                            <div class="md:col-span-2">
                                <label class="block text-sm font-medium text-gray-700"
                                    >Description</label
                                >
                                <textarea
                                    v-model="newProduct.description"
                                    rows="4"
                                    required
                                    class="mt-1 block w-full py-2 px-3 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
                                ></textarea>
                            </div>
                            <div class="md:col-span-2">
                                <label class="block text-sm font-medium text-gray-700"
                                    >Product Image</label
                                >
                                <div
                                    class="mt-1 flex justify-center px-6 pt-5 pb-6 border-2 border-gray-300 border-dashed rounded-md"
                                >
                                    <div class="space-y-1 text-center">
                                        <svg
                                            class="mx-auto h-12 w-12 text-gray-400"
                                            stroke="currentColor"
                                            fill="none"
                                            viewBox="0 0 48 48"
                                        >
                                            <path
                                                d="M28 8H12a4 4 0 00-4 4v20m32-12v8m0 0v8a4 4 0 01-4 4H12a4 4 0 01-4-4v-4m32-4l-3.172-3.172a4 4 0 00-5.656 0L28 28M8 32l9.172-9.172a4 4 0 015.656 0L28 28m0 0l4 4m4-24h8m-4-4v8m-12 4h.02"
                                                stroke-width="2"
                                                stroke-linecap="round"
                                                stroke-linejoin="round"
                                            />
                                        </svg>
                                        <div class="flex text-sm text-gray-600">
                                            <label
                                                for="file-upload"
                                                class="relative cursor-pointer bg-white rounded-md font-medium text-indigo-600 hover:text-indigo-500 focus-within:outline-none focus-within:ring-2 focus-within:ring-offset-2 focus-within:ring-indigo-500"
                                            >
                                                <span>Upload a file</span>
                                                <input
                                                    id="file-upload"
                                                    name="file-upload"
                                                    type="file"
                                                    class="sr-only"
                                                    @change="handleFileUpload"
                                                />
                                            </label>
                                            <p class="pl-1">or drag and drop</p>
                                        </div>
                                        <p class="text-xs text-gray-500">
                                            PNG, JPG, GIF up to 10MB
                                        </p>
                                    </div>
                                </div>
                                <div v-if="previewImage" class="mt-3">
                                    <img
                                        :src="previewImage"
                                        alt="Preview"
                                        class="h-32 object-contain"
                                    />
                                </div>
                            </div>
                        </div>
                        <div class="mt-6">
                            <button
                                type="submit"
                                class="w-full bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-2 px-4 rounded"
                            >
                                Register Product
                            </button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
        <div
            v-if="notification.show"
            class="fixed bottom-4 right-4 bg-gray-800 text-white px-4 py-2 rounded shadow-lg"
        >
            {{ notification.message }}
        </div>
    </div>
</template>

<script>
import { ref, onMounted, computed, watch } from "vue";
import { AgGridVue } from "ag-grid-vue3";
import "ag-grid-community/styles/ag-grid.css";
import "ag-grid-community/styles/ag-theme-alpine.css";
import Chart from "chart.js/auto";

export default {
    name: "DashboardComponent",
    components: {
        AgGridVue,
    },
    setup() {
        const activeTab = ref('revenue');
        const notification = ref({ show: false, message: '' });
        const timeFilter = ref('monthly');
        const categoryFilter = ref('all');
        const platformFilter = ref('all');
        const totalOrders = ref(5823);
        const totalRevenue = ref(429750);
        const orderGrowth = ref(12.4);
        const revenueGrowth = ref(15.8);
        const averageOrderValue = ref(73.8);
        const aovGrowth = ref(3.2);
        const conversionRate = ref(3.7);
        const conversionDrop = ref(0.5);
        let revenueChart = null;
        let ordersChart = null;
        let categoryChart = null;
        const searchTerm = ref('');
        const inventoryCategoryFilter = ref('all');
        const stockFilter = ref('all');
        const products = ref([
            { id: 1, name: 'Smartphone XYZ', sku: 'EL-SP-001', category: 'electronics', price: 499.99, stock: 124, platform: 'amazon', reorderPoint: 20, description: 'Latest smartphone with advanced features' },
            { id: 2, name: 'Wireless Headphones', sku: 'EL-HP-002', category: 'electronics', price: 89.99, stock: 76, platform: 'walmart', reorderPoint: 15, description: 'Noise cancelling wireless headphones' },
            { id: 3, name: 'Men\'s Running Shoes', sku: 'CL-SH-003', category: 'clothing', price: 79.99, stock: 45, platform: 'amazon', reorderPoint: 10, description: 'Comfortable running shoes for men' },
            { id: 4, name: 'Women\'s Summer Dress', sku: 'CL-DR-004', category: 'clothing', price: 49.99, stock: 32, platform: 'both', reorderPoint: 8, description: 'Light summer dress for women' },
            { id: 5, name: 'Smart Coffee Maker', sku: 'HK-CF-005', category: 'home', price: 129.99, stock: 18, platform: 'walmart', reorderPoint: 5, description: 'Programmable coffee maker with smart features' },
            { id: 6, name: 'Kitchen Blender', sku: 'HK-BL-006', category: 'home', price: 69.99, stock: 7, platform: 'amazon', reorderPoint: 10, description: 'High-speed blender for kitchen use' },
            { id: 7, name: 'Face Moisturizer', sku: 'BC-FM-007', category: 'beauty', price: 24.99, stock: 52, platform: 'both', reorderPoint: 15, description: 'Hydrating face moisturizer for all skin types' },
            { id: 8, name: 'Hair Styling Kit', sku: 'BC-HK-008', category: 'beauty', price: 59.99, stock: 0, platform: 'walmart', reorderPoint: 5, description: 'Complete hair styling kit with accessories' },
            { id: 9, name: 'Tablet Pro', sku: 'EL-TB-009', category: 'electronics', price: 349.99, stock: 29, platform: 'amazon', reorderPoint: 10, description: '10-inch tablet with high-resolution display' },
            { id: 10, name: 'Smart Watch', sku: 'EL-SW-010', category: 'electronics', price: 199.99, stock: 41, platform: 'both', reorderPoint: 12, description: 'Fitness tracking smart watch with notifications' },
            { id: 11, name: 'Bedside Lamp', sku: 'HK-LM-011', category: 'home', price: 34.99, stock: 13, platform: 'walmart', reorderPoint: 8, description: 'Modern bedside lamp with multiple light modes' },
            { id: 12, name: 'Baby Shampoo', sku: 'BC-BS-012', category: 'beauty', price: 9.99, stock: 86, platform: 'amazon', reorderPoint: 20, description: 'Gentle baby shampoo with natural ingredients' },
        ]);
        let inventoryGrid = null;
        const newProduct = ref({
            name: '',
            sku: '',
            category: 'electronics',
            platform: 'amazon',
            price: '',
            stock: '',
            description: '',
            reorderPoint: 10,
        });
        const previewImage = ref(null);
        const totalProducts = computed(() => products.value.length);
        const inStockProducts = computed(() => products.value.filter(p => p.stock > p.reorderPoint).length);
        const lowStockProducts = computed(() => products.value.filter(p => p.stock > 0 && p.stock <= p.reorderPoint).length);
        const outOfStockProducts = computed(() => products.value.filter(p => p.stock === 0).length);

        function showNotification(message) {
            notification.value = { show: true, message };
            setTimeout(() => {
                notification.value.show = false;
            }, 3000);
        }

        function applyFilters() {
            showNotification(`Filters applied: ${timeFilter.value} / ${categoryFilter.value} / ${platformFilter.value}`);
            updateCharts();
        }

        function applyInventoryFilters() {
            showNotification('Inventory filters applied');
            updateInventoryGrid();
        }

        function handleFileUpload(event) {
            const file = event.target.files[0];
            if (file) {
                previewImage.value = URL.createObjectURL(file);
            }
        }

        function registerProduct() {
            const newId = Math.max(...products.value.map(p => p.id), 0) + 1;
            const product = { ...newProduct.value, id: newId };
            products.value.push(product);
            newProduct.value = {
                name: '',
                sku: '',
                category: 'electronics',
                platform: 'amazon',
                price: '',
                stock: '',
                description: '',
                reorderPoint: 10,
            };
            previewImage.value = null;
            showNotification('Product successfully registered!');
            if (inventoryGrid) {
                inventoryGrid.api.setRowData(products.value);
            }
        }

        function updateInventoryValue(data) {
            const product = products.value.find(p => p.id === data.id);
            if (product) {
                product.stock = parseInt(data.newValue);
                showNotification(`Updated stock for ${product.name} to ${data.newValue}`);
            }
        }

        function updateCharts() {
            let labels, revenueData, ordersData;
            switch(timeFilter.value) {
                case 'daily':
                    labels = ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'];
                    revenueData = [12500, 15000, 11000, 13500, 21000, 18500, 16000];
                    ordersData = [180, 210, 160, 190, 280, 250, 225];
                    break;
                case 'weekly':
                    labels = ['Week 1', 'Week 2', 'Week 3', 'Week 4'];
                    revenueData = [82000, 95000, 110000, 93500];
                    ordersData = [1120, 1350, 1480, 1290];
                    break;
                case 'monthly':
                    labels = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun'];
                    revenueData = [310000, 290000, 350000, 330000, 410000, 429750];
                    ordersData = [4200, 3900, 4800, 4500, 5600, 5823];
                    break;
                case 'yearly':
                    labels = ['2020', '2021', '2022', '2023', '2024'];
                    revenueData = [2100000, 2450000, 3200000, 3850000, 4100000];
                    ordersData = [28500, 33200, 43500, 52300, 55900];
                    break;
            }
            if (revenueChart) {
                revenueChart.data.labels = labels;
                revenueChart.data.datasets[0].data = revenueData;
                revenueChart.update();
            }
            if (ordersChart) {
                ordersChart.data.labels = ['Amazon', 'Walmart'];
                let amazonOrders, walmartOrders;
                switch(categoryFilter.value) {
                    case 'electronics':
                        amazonOrders = 2100;
                        walmartOrders = 1500;
                        break;
                    case 'clothing':
                        amazonOrders = 950;
                        walmartOrders = 750;
                        break;
                    case 'home':
                        amazonOrders = 600;
                        walmartOrders = 800;
                        break;
                    case 'beauty':
                        amazonOrders = 450;
                        walmartOrders = 350;
                        break;
                    default:
                        amazonOrders = 3450;
                        walmartOrders = 2373;
                        break;
                }
                ordersChart.data.datasets[0].data = [amazonOrders, walmartOrders];
                ordersChart.update();
            }
            if (categoryChart) {
                let categoryData;
                switch(platformFilter.value) {
                    case 'amazon':
                        categoryData = [150000, 65000, 42000, 35000];
                        break;
                    case 'walmart':
                        categoryData = [120000, 45000, 50000, 30000];
                        break;
                    default:
                        categoryData = [270000, 110000, 92000, 65000];
                        break;
                }
                categoryChart.data.datasets[0].data = categoryData;
                categoryChart.update();
            }
        }

        function setupInventoryGrid() {
            const columnDefs = [
                { headerName: 'ID', field: 'id', sortable: true, filter: true, width: 70 },
                { headerName: 'Product Name', field: 'name', sortable: true, filter: true, flex: 2 },
                { headerName: 'SKU', field: 'sku', sortable: true, filter: true, width: 120 },
                {
                    headerName: 'Category',
                    field: 'category',
                    sortable: true,
                    filter: true,
                    width: 150,
                    cellRenderer: params => {
                        const capitalizedCategory = params.value.charAt(0).toUpperCase() + params.value.slice(1);
                        return capitalizedCategory;
                    }
                },
                {
                    headerName: 'Price',
                    field: 'price',
                    sortable: true,
                    filter: true,
                    width: 120,
                    cellRenderer: params => {
                        return `${params.value.toFixed(2)}`;
                    }
                },
                {
                    headerName: 'Platform',
                    field: 'platform',
                    sortable: true,
                    filter: true,
                    width: 120,
                    cellRenderer: params => {
                        const platform = params.value;
                        if (platform === 'both') {
                            return 'Amazon & Walmart';
                        }
                        return platform.charAt(0).toUpperCase() + platform.slice(1);
                    }
                },
                {
                    headerName: 'Stock',
                    field: 'stock',
                    sortable: true,
                    filter: true,
                    width: 120,
                    editable: true,
                    cellRenderer: params => {
                        const stock = params.value;
                        const reorderPoint = params.data.reorderPoint;
                        let cellClass = '';
                        if (stock === 0) {
                            cellClass = 'bg-red-100 text-red-700 font-bold';
                        } else if (stock <= reorderPoint) {
                            cellClass = 'bg-yellow-100 text-yellow-700 font-bold';
                        }
                        return `<div class="${cellClass}" style="padding: 5px; border-radius: 4px;">${stock}</div>`;
                    }
                },
                {
                    headerName: 'Status',
                    field: 'stock',
                    sortable: true,
                    filter: true,
                    width: 150,
                    cellRenderer: params => {
                        const stock = params.value;
                        const reorderPoint = params.data.reorderPoint;
                        if (stock === 0) {
                            return '<span class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium bg-red-100 text-red-800">Out of Stock</span>';
                        } else if (stock <= reorderPoint) {
                            return '<span class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium bg-yellow-100 text-yellow-800">Low Stock</span>';
                        } else {
                            return '<span class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium bg-green-100 text-green-800">In Stock</span>';
                        }
                    }
                },
                {
                    headerName: 'Action',
                    width: 120,
                    cellRenderer: params => {
                        return `<button class="bg-indigo-600 hover:bg-indigo-700 text-white text-xs py-1 px-2 rounded" data-action="update" data-id="${params.data.id}">Update Stock</button>`;
                    }
                }
            ];
            const gridOptions = {
                columnDefs: columnDefs,
                rowData: products.value,
                pagination: true,
                paginationPageSize: 10,
                domLayout: 'autoHeight',
                onCellValueChanged: (event) => {
                    if (event.colDef.field === 'stock') {
                        updateInventoryValue({
                            id: event.data.id,
                            newValue: event.newValue
                        });
                    }
                },
                onCellClicked: (params) => {
                    if (params.column.colId === 'Action') {
                        const action = params.event.target.getAttribute('data-action');
                        const id = parseInt(params.event.target.getAttribute('data-id'));
                        if (action === 'update') {
                            const product = products.value.find(p => p.id === id);
                            if (product) {
                                const newStock = prompt(`Update stock for ${product.name}:`, product.stock);
                                if (newStock !== null && !isNaN(parseInt(newStock))) {
                                    product.stock = parseInt(newStock);
                                    inventoryGrid.api.refreshCells();
                                    showNotification(`Updated stock for ${product.name} to ${newStock}`);
                                }
                            }
                        }
                    }
                }
            };
            const gridDiv = document.getElementById('inventoryGrid');
            inventoryGrid = new agGrid.Grid(gridDiv, gridOptions);
        }

        function updateInventoryGrid() {
            if (!inventoryGrid) return;
            let filteredProducts = [...products.value];
            if (searchTerm.value) {
                const search = searchTerm.value.toLowerCase();
                filteredProducts = filteredProducts.filter(p =>
                    p.name.toLowerCase().includes(search) ||
                    p.sku.toLowerCase().includes(search) ||
                    p.description.toLowerCase().includes(search)
                );
            }
            if (inventoryCategoryFilter.value !== 'all') {
                filteredProducts = filteredProducts.filter(p => p.category === inventoryCategoryFilter.value);
            }
            if (stockFilter.value !== 'all') {
                switch(stockFilter.value) {
                    case 'instock':
                        filteredProducts = filteredProducts.filter(p => p.stock > p.reorderPoint);
                        break;
                    case 'low':
                        filteredProducts = filteredProducts.filter(p => p.stock > 0 && p.stock <= p.reorderPoint);
                        break;
                    case 'outofstock':
                        filteredProducts = filteredProducts.filter(p => p.stock === 0);
                        break;
                }
            }
            inventoryGrid.api.setRowData(filteredProducts);
        }

        function setupRevenueChart() {
            const ctx = document.getElementById('revenueChart').getContext('2d');
            revenueChart = new Chart(ctx, {
                type: 'line',
                data: {
                    labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun'],
                    datasets: [
                        {
                            label: 'Revenue',
                            data: [310000, 290000, 350000, 330000, 410000, 429750],
                            borderColor: 'rgb(79, 70, 229)',
                            tension: 0.1,
                            fill: false
                        }
                    ]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: {
                        y: {
                            beginAtZero: true,
                            ticks: {
                                callback: function(value) {
                                    return value.toLocaleString();
                                }
                            }
                        }
                    }
                }
            });
        }

        function setupOrdersChart() {
            const ctx = document.getElementById('ordersChart').getContext('2d');
            ordersChart = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: ['Amazon', 'Walmart'],
                    datasets: [
                        {
                            label: 'Orders',
                            data: [3450, 2373],
                            backgroundColor: [
                                'rgba(79, 70, 229, 0.6)',
                                'rgba(245, 158, 11, 0.6)'
                            ]
                        }
                    ]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    indexAxis: 'y',
                    scales: {
                        x: {
                            beginAtZero: true
                        }
                    }
                }
            });
        }

        function setupCategoryChart() {
            const ctx = document.getElementById('categoryChart').getContext('2d');
            categoryChart = new Chart(ctx, {
                type: 'doughnut',
                data: {
                    labels: ['Electronics', 'Clothing', 'Home & Kitchen', 'Beauty & Personal Care'],
                    datasets: [
                        {
                            data: [270000, 110000, 92000, 65000],
                            backgroundColor: [
                                'rgba(79, 70, 229, 0.6)',
                                'rgba(16, 185, 129, 0.6)',
                                'rgba(245, 158, 11, 0.6)',
                                'rgba(236, 72, 153, 0.6)'
                            ]
                        }
                    ]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: {
                            position: 'bottom'
                        },
                        tooltip: {
                            callbacks: {
                                label: function(context) {
                                    const value = context.raw;
                                    const total = context.dataset.data.reduce((a, b) => a + b, 0);
                                    const percentage = Math.round((value / total) * 100);
                                    return `${value.toLocaleString()} (${percentage}%)`;
                                }
                            }
                        }
                    }
                }
            });
        }

        onMounted(() => {
            if (activeTab.value === 'revenue') {
                setupRevenueChart();
                setupOrdersChart();
                setupCategoryChart();
            }
            if (activeTab.value === 'inventory') {
                setupInventoryGrid();
            }
        });

        watch(activeTab, (newValue) => {
            if (newValue === 'revenue') {
                setTimeout(() => {
                    if (!revenueChart) setupRevenueChart();
                    if (!ordersChart) setupOrdersChart();
                    if (!categoryChart) setupCategoryChart();
                }, 100);
            } else if (newValue === 'inventory') {
                setTimeout(() => {
                    if (!inventoryGrid) setupInventoryGrid();
                }, 100);
            }
        });

        return {
            activeTab,
            notification,
            showNotification,
            timeFilter,
            categoryFilter,
            platformFilter,
            totalOrders,
            totalRevenue,
            orderGrowth,
            revenueGrowth,
            averageOrderValue,
            aovGrowth,
            conversionRate,
            conversionDrop,
            applyFilters,
            searchTerm,
            inventoryCategoryFilter,
            stockFilter,
            products,
            totalProducts,
            inStockProducts,
            lowStockProducts,
            outOfStockProducts,
            applyInventoryFilters,
            newProduct,
            previewImage,
            handleFileUpload,
            registerProduct
        };
    },
};
</script>

<style>
[v-cloak] {
    display: none;
}
.active-tab {
    border-bottom: 3px solid #4f46e5;
}
.card {
    transition: all 0.3s ease;
}
.card:hover {
    transform: translateY(-5px);
    box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1);
}
</style>
