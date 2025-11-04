<script setup lang="ts">
import { ref, computed } from 'vue'

const activeTab = ref('swap')
const sellAmount = ref('0')
const buyAmount = ref('0')

// Token management
const sellToken = ref('QUBIC')
const buyToken = ref('USDT')

// Modal state
const isTokenModalOpen = ref(false)
const selectedTokenType = ref<'sell' | 'buy'>('sell')
const searchQuery = ref('')

// Token data
const quickTokens = [
  { symbol: 'ETH', name: 'Ethereum', color: 'from-blue-500 to-purple-600', address: '0x...' },
  { symbol: 'USDC', name: 'USD Coin', color: 'from-blue-400 to-blue-600', address: '0x...' },
  { symbol: 'WETH', name: 'Wrapped ETH', color: 'from-gray-400 to-gray-600', address: '0x...' },
]

const recentSearches = ref([
  { symbol: 'SOL', name: 'Solana', color: 'from-purple-500 to-pink-600', address: '0xbdE8...aB97' },
])

const popularTokens = [
  { symbol: 'QUBIC', name: 'Qubic', color: 'from-blue-500 to-purple-600', address: '0x...', volume: '2.5M' },
  { symbol: 'ETH', name: 'Unichain ETH', color: 'from-blue-500 to-purple-600', address: '0x...', volume: '1.2M' },
  { symbol: 'USDC', name: 'USDC', color: 'from-blue-400 to-blue-600', address: '0x078D...7AD6', volume: '890K' },
  { symbol: 'USDT', name: 'USDTO', color: 'from-green-500 to-emerald-600', address: '0x9151...EcC5', volume: '756K' },
  { symbol: 'HYPE', name: 'Hyperliquid', color: 'from-teal-500 to-cyan-600', address: '0x15D0...f68d', volume: '543K' },
  { symbol: 'WBTC', name: 'Wrapped Bitcoin', color: 'from-orange-400 to-orange-600', address: '0x0555...2B9c', volume: '432K' },
  { symbol: 'SOL', name: 'Solana', color: 'from-purple-500 to-pink-600', address: '0x...', volume: '321K' },
]

const filteredTokens = computed(() => {
  if (!searchQuery.value) return popularTokens
  const query = searchQuery.value.toLowerCase()
  return popularTokens.filter(token => 
    token.name.toLowerCase().includes(query) || 
    token.symbol.toLowerCase().includes(query)
  )
})

const openTokenModal = (type: 'sell' | 'buy') => {
  selectedTokenType.value = type
  isTokenModalOpen.value = true
  searchQuery.value = ''
}

const closeTokenModal = () => {
  isTokenModalOpen.value = false
  searchQuery.value = ''
}

const selectToken = (token: { symbol: string; name: string; color: string }) => {
  if (selectedTokenType.value === 'sell') {
    sellToken.value = token.symbol
  } else {
    buyToken.value = token.symbol
  }
  closeTokenModal()
  
  // Add to recent searches if not already there
  if (!recentSearches.value.some(t => t.symbol === token.symbol)) {
    recentSearches.value.unshift({
      symbol: token.symbol,
      name: token.name,
      color: token.color,
      address: '0x...'
    })
    if (recentSearches.value.length > 5) {
      recentSearches.value.pop()
    }
  }
}

const clearRecentSearches = () => {
  recentSearches.value = []
}

const swapTokens = () => {
  // Swap tokens
  const tempToken = sellToken.value
  sellToken.value = buyToken.value
  buyToken.value = tempToken
  
  // Swap amounts
  const tempAmount = sellAmount.value
  sellAmount.value = buyAmount.value
  buyAmount.value = tempAmount
}

const getTokenColor = (token: string) => {
  const tokenData = [...quickTokens, ...popularTokens, ...recentSearches.value].find(t => t.symbol === token)
  if (tokenData) return tokenData.color
  
  if (token === 'QUBIC') {
    return 'from-blue-500 to-purple-600'
  } else if (token === 'USDT') {
    return 'from-green-500 to-emerald-600'
  }
  return 'from-gray-500 to-gray-600'
}
</script>

<template>
  <div class="min-h-screen bg-[#0d1117] flex items-center justify-center p-4 relative">
    <div class="w-full max-w-md relative">
      <!-- Navigation Tabs -->
      <div class="flex items-center justify-between mb-6">
        <div class="flex gap-6">
          <button
            v-for="tab in ['swap', 'limit', 'buy', 'sell']"
            :key="tab"
            @click="activeTab = tab"
            :class="[
              'text-sm font-medium transition-colors',
              activeTab === tab
                ? 'text-white'
                : 'text-gray-500 hover:text-gray-400'
            ]"
          >
            {{ tab === 'swap' ? 'Swap' : tab === 'limit' ? 'Limit' : tab === 'buy' ? 'Buy' : 'Sell' }}
          </button>
        </div>
        <button class="text-gray-400 hover:text-gray-300 transition-colors">
          <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z" />
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
          </svg>
        </button>
      </div>

      <!-- Swap Card -->
      <div class="bg-[#161b22] border border-gray-800 rounded-2xl p-6 space-y-4">
        <!-- Sell Section -->
        <div class="space-y-2">
          <div class="flex items-center justify-between">
            <label class="text-sm text-gray-400">Sell</label>
          </div>
          <div class="flex items-center justify-between bg-[#0d1117] border border-gray-800 rounded-xl p-4">
            <div class="flex-1">
              <input
                v-model="sellAmount"
                type="text"
                class="w-full bg-transparent text-2xl font-semibold text-white outline-none placeholder-gray-600"
                placeholder="0"
              />
              <div class="text-sm text-gray-500 mt-1">0 US$</div>
            </div>
            <button @click="openTokenModal('sell')" class="flex items-center gap-2 bg-[#161b22] border border-gray-700 rounded-lg px-4 py-2 hover:bg-[#1f2937] transition-colors">
              <div :class="`w-6 h-6 rounded-full bg-gradient-to-br ${getTokenColor(sellToken)}`"></div>
              <span class="text-white font-medium">{{ sellToken }}</span>
              <svg class="w-4 h-4 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7" />
              </svg>
            </button>
          </div>
        </div>

        <!-- Swap Arrow Button -->
        <div class="flex justify-center -my-2 relative z-10">
          <button 
            @click="swapTokens"
            class="w-10 h-10 bg-[#0d1117] border-2 border-gray-800 rounded-full flex items-center justify-center hover:border-gray-700 transition-colors cursor-pointer"
          >
            <svg class="w-5 h-5 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 14l-7 7m0 0l-7-7m7 7V3" />
            </svg>
          </button>
        </div>

        <!-- Buy Section -->
        <div class="space-y-2">
          <div class="flex items-center justify-between">
            <label class="text-sm text-gray-400">Buy</label>
          </div>
          <div class="flex items-center justify-between bg-[#0d1117] border border-gray-800 rounded-xl p-4">
            <div class="flex-1">
              <input
                v-model="buyAmount"
                type="text"
                class="w-full bg-transparent text-2xl font-semibold text-white outline-none placeholder-gray-600"
                placeholder="0"
              />
              <div class="text-sm text-gray-500 mt-1">0 US$</div>
            </div>
            <button @click="openTokenModal('buy')" class="flex items-center gap-2 bg-[#161b22] border border-gray-700 rounded-lg px-4 py-2 hover:bg-[#1f2937] transition-colors">
              <div :class="`w-6 h-6 rounded-full bg-gradient-to-br ${getTokenColor(buyToken)}`"></div>
              <span class="text-white font-medium">{{ buyToken }}</span>
              <svg class="w-4 h-4 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7" />
              </svg>
            </button>
          </div>
        </div>

        <!-- Connect Wallet Button -->
        <button class="w-full bg-green-500 hover:bg-green-600 text-white font-semibold py-4 rounded-xl transition-colors mt-6">
          Connect Wallet
        </button>
      </div>
    </div>

    <!-- Token Selection Modal -->
    <Transition name="slide">
      <div v-if="isTokenModalOpen" class="fixed inset-0 z-50 flex items-center justify-end">
        <!-- Backdrop -->
        <div @click="closeTokenModal" class="absolute inset-0 bg-black/50"></div>
        
        <!-- Modal Content -->
        <div class="relative w-full max-w-md h-full bg-[#161b22] border-l border-gray-800 shadow-2xl overflow-y-auto">
          <!-- Header -->
          <div class="sticky top-0 bg-[#161b22] border-b border-gray-800 p-4 flex items-center justify-between z-10">
            <h2 class="text-xl font-semibold text-white">Select token</h2>
            <button @click="closeTokenModal" class="text-gray-400 hover:text-white transition-colors">
              <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
              </svg>
            </button>
          </div>

          <div class="p-4 space-y-6">
            <!-- Search Bar -->
            <div class="relative">
              <div class="absolute left-3 top-1/2 -translate-y-1/2 text-gray-400">
                <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
                </svg>
              </div>
              <input
                v-model="searchQuery"
                type="text"
                placeholder="Search token"
                class="w-full bg-[#0d1117] border border-gray-800 rounded-lg pl-10 pr-20 py-3 text-white placeholder-gray-500 focus:outline-none focus:border-gray-700"
              />
              <div class="absolute right-3 top-1/2 -translate-y-1/2 flex items-center gap-2">
                <button class="text-pink-500 hover:text-pink-400">
                  <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24">
                    <path d="M12 2l3.09 6.26L22 9.27l-5 4.87 1.18 6.88L12 17.77l-6.18 3.25L7 14.14 2 9.27l6.91-1.01L12 2z" />
                  </svg>
                </button>
                <svg class="w-4 h-4 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7" />
                </svg>
              </div>
            </div>

            <!-- Quick Select Tokens -->
            <div>
              <div class="flex gap-3">
                <button
                  v-for="token in quickTokens"
                  :key="token.symbol"
                  @click="selectToken(token)"
                  class="flex-1 flex flex-col items-center gap-2 bg-[#0d1117] border border-gray-800 rounded-lg p-4 hover:border-gray-700 transition-colors"
                >
                  <div class="relative">
                    <div :class="`w-12 h-12 rounded-full bg-gradient-to-br ${token.color}`"></div>
                    <div class="absolute -top-1 -right-1 w-5 h-5 bg-pink-500 rounded-full flex items-center justify-center">
                      <span class="text-white text-xs font-bold">+</span>
                    </div>
                  </div>
                  <span class="text-white font-medium text-sm">{{ token.symbol }}</span>
                </button>
              </div>
            </div>

            <!-- Recent Searches -->
            <div v-if="recentSearches.length > 0">
              <div class="flex items-center justify-between mb-3">
                <div class="flex items-center gap-2">
                  <svg class="w-5 h-5 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
                  </svg>
                  <h3 class="text-sm font-medium text-gray-400">Recent searches</h3>
                </div>
                <button @click="clearRecentSearches" class="text-sm text-gray-500 hover:text-gray-400 transition-colors">
                  Clear
                </button>
              </div>
              <div class="space-y-2">
                <button
                  v-for="token in recentSearches"
                  :key="token.symbol"
                  @click="selectToken(token)"
                  class="w-full flex items-center gap-3 p-3 bg-[#0d1117] border border-gray-800 rounded-lg hover:border-gray-700 transition-colors"
                >
                  <div class="relative">
                    <div :class="`w-10 h-10 rounded-full bg-gradient-to-br ${token.color}`"></div>
                    <div class="absolute -top-1 -right-1 w-5 h-5 bg-pink-500 rounded-full flex items-center justify-center">
                      <span class="text-white text-xs font-bold">+</span>
                    </div>
                  </div>
                  <div class="flex-1 text-left">
                    <div class="text-white font-medium">{{ token.name }}</div>
                    <div class="text-sm text-gray-500">{{ token.symbol }} · {{ token.address }}</div>
                  </div>
                </button>
              </div>
            </div>

            <!-- Tokens by 24H Trading Volume -->
            <div>
              <div class="flex items-center gap-2 mb-3">
                <svg class="w-5 h-5 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z" />
                </svg>
                <h3 class="text-sm font-medium text-gray-400">Tokens by 24H trading volume</h3>
              </div>
              <div class="space-y-2">
                <button
                  v-for="token in filteredTokens"
                  :key="token.symbol"
                  @click="selectToken(token)"
                  class="w-full flex items-center gap-3 p-3 bg-[#0d1117] border border-gray-800 rounded-lg hover:border-gray-700 transition-colors"
                >
                  <div class="relative">
                    <div :class="`w-10 h-10 rounded-full bg-gradient-to-br ${token.color}`"></div>
                    <div class="absolute -top-1 -right-1 w-5 h-5 bg-pink-500 rounded-full flex items-center justify-center">
                      <span class="text-white text-xs font-bold">+</span>
                    </div>
                  </div>
                  <div class="flex-1 text-left">
                    <div class="text-white font-medium">{{ token.name }}</div>
                    <div class="text-sm text-gray-500">{{ token.symbol }}{{ token.address ? ` · ${token.address}` : '' }}</div>
                  </div>
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </Transition>
  </div>
</template>

<style scoped>
.slide-enter-active,
.slide-leave-active {
  transition: all 0.3s ease;
}

.slide-enter-from {
  opacity: 0;
}

.slide-enter-from > div:last-child {
  transform: translateX(100%);
}

.slide-leave-to {
  opacity: 0;
}

.slide-leave-to > div:last-child {
  transform: translateX(100%);
}
</style>
