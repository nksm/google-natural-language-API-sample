<script setup lang="ts">
import { ref } from 'vue'

interface SentenceSentiment {
  text: string
  score: number
  magnitude: number
}

interface AnalysisResult {
  documentScore: number
  documentMagnitude: number
  sentences: SentenceSentiment[]
}

const API_KEY = import.meta.env.VITE_GOOGLE_API_KEY
const text = ref('')
const loading = ref(false)
const result = ref<AnalysisResult | null>(null)

const analyzeSentiment = async () => {
  loading.value = true
  try {
    const response = await fetch(
      `https://language.googleapis.com/v1/documents:analyzeSentiment?key=${API_KEY}`,
      {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          document: {
            type: 'PLAIN_TEXT',
            content: text.value
          }
        })
      }
    )
    const data = await response.json()
    result.value = {
      documentScore: data.documentSentiment.score,
      documentMagnitude: data.documentSentiment.magnitude,
      sentences: data.sentences.map((sentence: any) => ({
        text: sentence.text.content,
        score: sentence.sentiment.score,
        magnitude: sentence.sentiment.magnitude
      }))
    }
  } catch (error) {
    console.error('Error:', error)
    alert('分析中にエラーが発生しました')
  } finally {
    loading.value = false
  }
}

const getSentimentColor = (score: number): string => {
  if (score > 0.25) return 'bg-green-100 text-green-800'
  if (score < -0.25) return 'bg-red-100 text-red-800'
  return 'bg-gray-100 text-gray-800'
}

const getSentimentText = (score: number): string => {
  if (score > 0.25) return 'ポジティブ'
  if (score < -0.25) return 'ネガティブ'
  return 'ニュートラル'
}
</script>

<template>
  <div class="w-full mx-auto flex gap-8 flex-col">
    <h1 class="text-2xl mb-4">Google Natural Language API sample</h1>
    <div class="w-full grid gap-12 grid-cols-2">
      <div class="w-full flex flex-col gap-1 items-center">
        <h2 class="text-xl self-start">分析する文章を入力してください</h2>
        <textarea
          v-model="text"
          class="w-full p-2 border rounded mb-4 field-sizing-content min-h-28 max-h-96"
          rows="4"
          placeholder="分析したいテキストを入力してください"
        />
        <button
          @click="analyzeSentiment"
          :disabled="loading"
          class="bg-blue-500 text-white px-4 py-2 rounded w-fit"
        >
          {{ loading ? '分析中...' : '分析する' }}
        </button>

      </div>
      <div v-if="result" class="flex flex-col gap-1">
        <h2 class="text-xl">分析結果</h2>
        <div class="flex flex-col gap-8">
          <div class="flex gap-2 flex-col">
            <h3 class="text-l font-bold">全体</h3>
            <div class="">
              <p>感情スコア: {{ result.documentScore.toFixed(2) }}</p>
              <p>感情の大きさ: {{ result.documentMagnitude.toFixed(2) }}</p>
              <p>判定: {{ getSentimentText(result.documentScore) }}</p>
            </div>
          </div>
          <div class="flex gap-2 flex-col">
            <h3 class="text-l font-bold">文節ごとの分析結果</h3>
            <div>
              <span
                v-for="(sentence, index) in result.sentences"
                :key="index"
                :class="[
                  'inline-block p-1 cursor-help transition-colors',
                  getSentimentColor(sentence.score)
                ]"
                class="group relative"
              >
                <span class="inline-block hover:font-bold">{{ sentence.text }}</span>
                <!-- ツールチップ -->
                <div class="absolute invisible group-hover:visible bg-white text-black text-sm rounded px-2 py-1 z-10 -top-20 left-1/2 transform -translate-x-1/2 min-w-[200px] whitespace-nowrap drop-shadow-md">
                  <div class="absolute -bottom-1 left-1/2 transform -translate-x-1/2 w-2 h-2 bg-white rotate-45"></div>
                  <p>感情スコア: {{ sentence.score.toFixed(2) }}</p>
                  <p>感情の大きさ: {{ sentence.magnitude.toFixed(2) }}</p>
                  <p>判定: {{ getSentimentText(sentence.score) }}</p>
                </div>
              </span>
            </div>
          </div>
        </div>
      </div>
    </div>

  </div>
</template>
