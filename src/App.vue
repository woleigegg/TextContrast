<template>
  <div class="min-h-screen">
    <!-- Header -->
    <header class="fixed top-0 left-0 right-0 bg-white/80 dark:bg-gray-900/80 backdrop-blur-lg border-b border-gray-200 dark:border-gray-800 z-50">
      <div class="container mx-auto px-4 h-16 flex items-center justify-between">
        <h1 class="text-2xl font-bold bg-gradient-to-r from-primary-500 to-primary-700 bg-clip-text text-transparent">
          Text Contrast
        </h1>
        <button
          @click="toggleTheme"
          class="p-2 rounded-lg hover:bg-gray-100 dark:hover:bg-gray-800 transition-colors"
          :aria-label="isDark ? 'Switch to light mode' : 'Switch to dark mode'"
        >
          <SunIcon v-if="isDark" class="w-6 h-6 text-gray-400" />
          <MoonIcon v-else class="w-6 h-6 text-gray-600" />
        </button>
      </div>
    </header>

    <!-- Main Content -->
    <main class="container mx-auto px-4 pt-24 pb-8">
      <div class="grid md:grid-cols-2 gap-4">
        <!-- Left Text Area -->
        <div class="space-y-2 animate-fade-in">
          <div class="flex items-center justify-between">
            <label class="text-lg font-medium">Original Text</label>
            <div class="flex items-center gap-4">
              <span class="text-sm text-gray-600 dark:text-gray-400">
                {{ leftText.length }} characters
              </span>
              <button 
                @click="clearLeft"
                class="btn hover:bg-gray-100 dark:hover:bg-gray-800"
              >
                <TrashIcon class="w-5 h-5" />
                Clear
              </button>
            </div>
          </div>
          <textarea
            ref="leftTextArea"
            v-model="leftText"
            class="text-area font-mono min-h-[400px]"
            placeholder="Enter or paste your original text here..."
            @input="highlightDifferences"
            @scroll="syncScroll('left')"
          ></textarea>
          <div class="mt-2 text-sm text-gray-600 dark:text-gray-400" v-if="differences.length > 0">
            <span class="text-red-500 dark:text-red-400">{{ removedCount }}</span> characters removed,
            <span class="text-green-500 dark:text-green-400">{{ addedCount }}</span> characters added
          </div>
        </div>

        <!-- Right Text Area -->
        <div class="space-y-2 animate-fade-in animation-delay-100">
          <div class="flex items-center justify-between">
            <label class="text-lg font-medium">Modified Text</label>
            <div class="flex items-center gap-4">
              <span class="text-sm text-gray-600 dark:text-gray-400">
                {{ rightText.length }} characters
              </span>
              <button 
                @click="clearRight"
                class="btn hover:bg-gray-100 dark:hover:bg-gray-800"
              >
                <TrashIcon class="w-5 h-5" />
                Clear
              </button>
            </div>
          </div>
          <textarea
            ref="rightTextArea"
            v-model="rightText"
            class="text-area font-mono min-h-[400px]"
            placeholder="Enter or paste your modified text here..."
            @input="highlightDifferences"
            @scroll="syncScroll('right')"
          ></textarea>
        </div>
      </div>

      <!-- Comparison Results -->
      <div class="mt-8 animate-fade-in animation-delay-200" v-if="differences.length > 0">
        <h2 class="text-xl font-semibold mb-4">Differences Found</h2>
        <div class="space-y-4">
          <div
            v-for="(diff, index) in differences"
            :key="index"
            class="p-4 rounded-lg bg-white dark:bg-gray-800 border border-gray-200 dark:border-gray-700 animate-slide-in"
            :style="{ animationDelay: `${index * 100}ms` }"
          >
            <div class="flex items-center justify-between mb-2">
              <span class="font-medium">Line {{ diff.lineNumber }}</span>
              <span class="text-sm text-gray-500 dark:text-gray-400">
                Click to locate
              </span>
            </div>
            <div 
              class="grid grid-cols-1 gap-4 cursor-pointer"
              @click="scrollToLine(diff.lineNumber)"
            >
              <div 
                v-if="diff.removed" 
                class="text-red-500 dark:text-red-400 bg-red-50 dark:bg-red-900/10 p-2 rounded group"
              >
                <div class="font-mono whitespace-pre-wrap">{{ diff.value }}</div>
              </div>
              <div 
                v-if="diff.added" 
                class="text-green-500 dark:text-green-400 bg-green-50 dark:bg-green-900/10 p-2 rounded group"
              >
                <div class="font-mono whitespace-pre-wrap">{{ diff.value }}</div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script setup>
import { ref, watch, onMounted, computed } from 'vue'
import { SunIcon, MoonIcon, TrashIcon } from '@heroicons/vue/24/outline'
import * as DiffMatchPatch from 'diff-match-patch'

const leftText = ref('')
const rightText = ref('')
const differences = ref([])
const isDark = ref(false)
const leftTextArea = ref(null)
const rightTextArea = ref(null)
const dmp = new DiffMatchPatch.diff_match_patch()

// Calculate added and removed counts
const addedCount = computed(() => {
  return differences.value
    .filter(d => d.added)
    .reduce((acc, curr) => acc + curr.value.length, 0)
})

const removedCount = computed(() => {
  return differences.value
    .filter(d => d.removed)
    .reduce((acc, curr) => acc + curr.value.length, 0)
})

// Synchronize scroll positions
const syncScroll = (source) => {
  const sourceElement = source === 'left' ? leftTextArea.value : rightTextArea.value
  const targetElement = source === 'left' ? rightTextArea.value : leftTextArea.value
  
  if (!sourceElement || !targetElement) return
  
  const sourceLines = sourceElement.value.split('\n').length
  const targetLines = targetElement.value.split('\n').length
  const ratio = targetLines / sourceLines
  
  targetElement.scrollTop = sourceElement.scrollTop * ratio
}

// Compare texts when either input changes
const highlightDifferences = () => {
  if (!leftText.value && !rightText.value) {
    differences.value = []
    return
  }

  const leftLines = leftText.value.split('\n')
  const rightLines = rightText.value.split('\n')
  const diffs = []
  
  leftLines.forEach((leftLine, lineIndex) => {
    const rightLine = rightLines[lineIndex] || ''
    // Skip if the only difference is whitespace or newlines
    if (leftLine.trim() !== rightLine.trim()) {
      const lineDiffs = dmp.diff_main(leftLine, rightLine)
      dmp.diff_cleanupSemantic(lineDiffs)
      
      // Filter out diffs that only contain whitespace or newlines
      const significantDiffs = lineDiffs.filter(([type, value]) => {
        return type === 0 || value.trim().length > 0
      })
      
      if (significantDiffs.length > 0) {
        significantDiffs.forEach(([type, value]) => {
          if (type !== 0) { // 0 means no difference
            diffs.push({
              lineNumber: lineIndex + 1,
              value: value,
              added: type === 1,
              removed: type === -1
            })
          }
        })
      }
    }
  })
  
  // Check for additional lines in the right text
  if (rightLines.length > leftLines.length) {
    rightLines.slice(leftLines.length).forEach((line, index) => {
      // Only add non-empty lines
      if (line.trim().length > 0) {
        diffs.push({
          lineNumber: leftLines.length + index + 1,
          value: line,
          added: true,
          removed: false
        })
      }
    })
  }
  
  differences.value = diffs
}

// Scroll to specific line
const scrollToLine = (lineNumber) => {
  const scrollToTextArea = (textArea) => {
    if (!textArea) return
    
    const lineHeight = parseInt(getComputedStyle(textArea).lineHeight)
    const targetPosition = (lineNumber - 1) * lineHeight
    
    textArea.scrollTop = targetPosition - textArea.clientHeight / 3
    
    // Highlight the line
    const lines = textArea.value.split('\n')
    const position = lines.slice(0, lineNumber - 1).join('\n').length
    const lineLength = lines[lineNumber - 1]?.length || 0
    
    textArea.focus()
    textArea.setSelectionRange(
      position > 0 ? position + 1 : 0,
      position + lineLength + 1
    )
  }
  
  scrollToTextArea(leftTextArea.value)
  scrollToTextArea(rightTextArea.value)
}

// Theme toggle
const toggleTheme = () => {
  isDark.value = !isDark.value
  document.documentElement.classList.toggle('dark')
  localStorage.setItem('theme', isDark.value ? 'dark' : 'light')
}

// Clear functions
const clearLeft = () => {
  leftText.value = ''
  highlightDifferences()
}
const clearRight = () => {
  rightText.value = ''
  highlightDifferences()
}

// Initialize theme
onMounted(() => {
  const theme = localStorage.getItem('theme')
  isDark.value = theme === 'dark' || 
    (!theme && window.matchMedia('(prefers-color-scheme: dark)').matches)
  
  if (isDark.value) {
    document.documentElement.classList.add('dark')
  }
})
</script>
