<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>福岡秋祭 2026 - 行程助手</title>
    <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@300;400;500;700&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Noto Sans TC', sans-serif; background-color: #F7F3F0; color: #333; }
        .muji-card { background: rgba(255, 255, 255, 0.8); backdrop-filter: blur(10px); border-radius: 16px; border: 1px solid #E5E1DA; }
        .active-tab { color: #8C7851; border-bottom: 3px solid #8C7851; }
        .hide-scrollbar::-webkit-scrollbar { display: none; }
        .fab { position: fixed; bottom: 24px; right: 24px; background: #8C7851; color: white; width: 56px; height: 56px; border-radius: 28px; display: flex; align-items: center; justify-content: center; box-shadow: 0 4px 12px rgba(140, 120, 81, 0.4); }
    </style>
</head>
<body>
    <div id="app" class="max-w-md mx-auto min-h-screen pb-24 relative">
        
        <!-- Header & Tabs -->
        <header class="relative h-48 bg-cover bg-center flex items-end p-4" style="background-image: url('https://images.unsplash.com/photo-1542051841857-5f90071e7989?auto=format&fit=crop&q=80');">
            <div class="absolute inset-0 bg-black/20"></div>
            <div class="relative w-full flex justify-between items-end">
                <h1 class="text-white text-2xl font-bold mb-2">福岡之旅 <span class="text-sm font-normal block">2026.10.19 - 10.24</span></h1>
                
                <!-- Tab Menu (浮動右下方) -->
                <nav class="muji-card flex space-x-4 px-4 py-2 mb-2">
                    <button @click="currentTab = 'itinerary'" :class="{'text-amber-800': currentTab === 'itinerary'}"><i data-lucide="map-pin"></i></button>
                    <button @click="currentTab = 'info'" :class="{'text-amber-800': currentTab === 'info'}"><i data-lucide="info"></i></button>
                    <button @click="currentTab = 'shopping'" :class="{'text-amber-800': currentTab === 'shopping'}"><i data-lucide="shopping-basket"></i></button>
                    <button @click="currentTab = 'expense'" :class="{'text-amber-800': currentTab === 'expense'}"><i data-lucide="circle-dollar-sign"></i></button>
                </nav>
            </div>
        </header>

        <!-- 內容區 -->
        <main class="p-4">
            
            <!-- 1. 每日行程表 -->
            <section v-if="currentTab === 'itinerary'">
                <!-- 天氣資訊 -->
                <div class="muji-card p-3 mb-4 flex items-center justify-between">
                    <div class="flex items-center">
                        <i data-lucide="cloud-sun" class="text-orange-400 mr-2"></i>
                        <span class="text-lg font-medium">福岡 22°C</span>
                    </div>
                    <div class="text-xs text-gray-500 text-right">體感 20°C<br>降雨機率 10%</div>
                </div>

                <!-- 日期橫向捲動 -->
                <div class="flex space-x-3 overflow-x-auto hide-scrollbar mb-6 pb-2">
                    <button v-for="d in dates" :key="d.date" 
                            @click="selectedDate = d.date"
                            :class="['flex-shrink-0 w-14 h-16 rounded-xl flex flex-col items-center justify-center transition-all', 
                                    selectedDate === d.date ? 'bg-[#8C7851] text-white shadow-lg' : 'bg-white text-gray-400']">
                        <span class="text-xs">{{ d.day }}</span>
                        <span class="text-lg font-bold">{{ d.num }}</span>
                    </button>
                </div>

                <!-- 行程卡片列表 -->
                <div class="space-y-4">
                    <div v-for="(item, index) in currentDaySchedule" :key="index">
                        <!-- 交通時間 (顯示在卡片之間) -->
                        <div v-if="index > 0" class="flex items-center justify-center my-2 text-xs text-gray-400">
                            <div class="border-l border-dashed border-gray-300 h-4 mr-2"></div>
                            <i data-lucide="car" class="w-3 h-3 mr-1"></i>
                            <span>約 25 分鐘</span>
                        </div>

                        <!-- 航班卡片 (特殊樣式) -->
                        <div v-if="item.type === 'flight'" class="muji-card border-l-4 border-amber-600 p-4 shadow-sm" @click="openNav(item.name)">
                            <div class="flex justify-between border-b pb-2 mb-2">
                                <span class="font-bold text-amber-800">{{item.from}} → {{item.to}}</span>
                                <span class="text-xs bg-amber-100 px-2 py-1 rounded text-amber-800">{{item.airline}}</span>
                            </div>
                            <div class="flex justify-between items-center px-4">
                                <div class="text-center"><div class="text-xl font-bold">{{item.depTime}}</div><div class="text-[10px] text-gray-400">{{item.fromCode}}</div></div>
                                <div class="text-gray-300">✈</div>
                                <div class="text-center"><div class="text-xl font-bold">{{item.arrTime}}</div><div class="text-[10px] text-gray-400">{{item.toCode}}</div></div>
                            </div>
                        </div>

                        <!-- 一般行程卡片 -->
                        <div v-else class="muji-card p-4 flex justify-between items-start group active:scale-95 transition-transform" @click="openNav(item.name)">
                            <div class="flex-1">
                                <div class="flex items-center mb-1">
                                    <span class="text-[10px] bg-gray-100 text-gray-500 px-1.5 py-0.5 rounded mr-2">{{item.time}}</span>
                                    <h3 class="font-bold text-gray-800">{{item.name}}</h3>
                                </div>
                                <p class="text-xs text-gray-500 mb-2">🕒 {{item.stay}} | {{item.note}}</p>
                                <div v-if="item.category === '美食'" class="inline-block text-[10px] border border-orange-200 text-orange-400 px-2 rounded-full">Restaurant</div>
                            </div>
                            <i data-lucide="chevron-right" class="text-gray-300 w-5 h-5"></i>
                        </div>
                    </div>
                </div>
                <button class="fab" @click="showAddModal = true"><i data-lucide="plus"></i></button>
            </section>

            <!-- 2. 資訊頁 -->
            <section v-if="currentTab === 'info'" class="space-y-4 animate-in fade-in">
                <!-- 匯率 -->
                <div class="muji-card p-4">
                    <h3 class="font-bold mb-3 flex items-center"><i data-lucide="refresh-cw" class="w-4 h-4 mr-2"></i> 匯率換算 (JMD/TWD)</h3>
                    <div class="flex items-center space-x-2">
                        <input type="number" v-model="jpyInput" class="w-full bg-gray-50 border-none rounded-lg p-2 text-right" placeholder="日幣">
                        <span>→</span>
                        <div class="w-full bg-[#8C7851]/10 rounded-lg p-2 text-right font-bold text-[#8C7851]">NT$ {{ (jpyInput * 0.21).toFixed(0) }}</div>
                    </div>
                </div>
                
                <!-- 住宿資訊 -->
                <div class="muji-card p-4">
                    <h3 class="font-bold mb-2">🏨 住宿資訊</h3>
                    <p class="text-sm">博多站東N.33飯店</p>
                    <p class="text-xs text-gray-500">福岡市博多區博多站東...</p>
                    <button @click="openNav('博多站東N.33飯店')" class="mt-2 text-xs text-blue-500 underline">Google Map 導航</button>
                </div>

                <!-- 緊急醫療 -->
                <div class="grid grid-cols-2 gap-3">
                    <a href="tel:110" class="muji-card p-4 text-center bg-red-50">
                        <i data-lucide="shield-alert" class="mx-auto mb-1 text-red-500"></i>
                        <span class="text-sm font-bold text-red-600">警察 110</span>
                    </a>
                    <a href="tel:119" class="muji-card p-4 text-center bg-orange-50">
                        <i data-lucide="ambulance" class="mx-auto mb-1 text-orange-500"></i>
                        <span class="text-sm font-bold text-orange-600">救護車 119</span>
                    </a>
                </div>
            </section>

            <!-- 3. 購物清單 -->
            <section v-if="currentTab === 'shopping'">
                <div class="grid grid-cols-2 gap-4">
                    <div v-for="item in shoppingList" :key="item.id" class="muji-card overflow-hidden">
                        <img :src="item.img" class="w-full h-32 object-cover">
                        <div class="p-2 text-sm font-medium">{{item.name}}</div>
                    </div>
                </div>
                <button class="fab" @click="alert('手機上傳照片功能已準備就緒')"><i data-lucide="plus"></i></button>
            </section>

            <!-- 4. 花費統計 -->
            <section v-if="currentTab === 'expense'">
                <div class="muji-card p-6 mb-4 text-center bg-[#8C7851] text-white">
                    <div class="text-sm opacity-80">總花費 (JPY)</div>
                    <div class="text-3xl font-bold">¥ {{ totalExpense }}</div>
                </div>
                <div class="space-y-2">
                    <div v-for="exp in expenses" :key="exp.id" class="muji-card p-3 flex justify-between items-center">
                        <div>
                            <div class="font-medium">{{exp.name}}</div>
                            <div class="text-[10px] text-gray-400">{{exp.date}} | {{exp.category}}</div>
                        </div>
                        <div class="font-bold text-red-500">- ¥{{exp.amount}}</div>
                    </div>
                </div>
                <button class="fab"><i data-lucide="plus"></i></button>
            </section>

        </main>
    </div>

    <script>
        const { createApp, ref, computed, onMounted, nextTick } = Vue;

        createApp({
            setup() {
                const currentTab = ref('itinerary');
                const selectedDate = ref('2026-10-19');
                const jpyInput = ref(1000);
                
                const dates = [
                    { day: 'Mon', num: '19', date: '2026-10-19' },
                    { day: 'Tue', num: '20', date: '2026-10-20' },
                    { day: 'Wed', num: '21', date: '2026-10-21' },
                    { day: 'Thu', num: '22', date: '2026-10-22' },
                    { day: 'Fri', num: '23', date: '2026-10-23' },
                    { day: 'Sat', num: '24', date: '2026-10-24' },
                ];

                const scheduleData = {
                    '2026-10-19': [
                        { type: 'flight', from: '高雄', to: '福岡', airline: '長榮航空 BR120', depTime: '15:30', arrTime: '19:20', fromCode: 'KHH', toCode: 'FUK' },
                        { type: 'spot', time: '20:41', name: '博多站', stay: '1時00分', note: '抵達飯店 Check-in', category: '住宿' },
                        { type: 'spot', time: '21:47', name: 'Shin-Shin 博多拉麵', stay: '1時00分', note: '必吃宵夜', category: '美食' }
                    ],
                    '2026-10-20': [
                        { type: 'spot', time: '09:00', name: '門司港車站', stay: '1時00分', note: '懷舊車站', category: '景點' },
                        { type: 'spot', time: '12:10', name: '唐戶市場', stay: '1時00分', note: '新鮮壽司', category: '美食' },
                        { type: 'spot', time: '15:10', name: '皿倉山展望台', stay: '1時00分', note: '新日本三大夜景', category: '景點' }
                    ]
                };

                const expenses = ref([
                    { id: 1, name: '機票', amount: 15000, category: '交通', date: '2026-10-19' },
                    { id: 2, name: '拉麵', amount: 1200, category: '飲食', date: '2026-10-19' }
                ]);

                const shoppingList = ref([
                    { id: 1, name: '藥妝 - 合利他命', img: 'https://images.unsplash.com/photo-1584308666744-24d5c474f2ae?auto=format&fit=crop&q=60' },
                    { id: 2, name: '一蘭拉麵包', img: 'https://images.unsplash.com/photo-1569718212165-3a8278d5f624?auto=format&fit=crop&q=60' }
                ]);

                const currentDaySchedule = computed(() => scheduleData[selectedDate.value] || []);
                const totalExpense = computed(() => expenses.value.reduce((sum, exp) => sum + exp.amount, 0));

                const openNav = (location) => {
                    window.open(`https://www.google.com/maps/dir/?api=1&destination=${encodeURIComponent(location)}`, '_blank');
                };

                onMounted(() => {
                    lucide.createIcons();
                });

                return {
                    currentTab, selectedDate, dates, currentDaySchedule, jpyInput, expenses, totalExpense, shoppingList, openNav
                };
            }
        }).mount('#app');
    </script>
</body>
</html>
