<template>
  <div class="min-h-screen flex flex-col items-center justify-start pt-8">
    <div class="w-full max-w-[600px] px-4">
      <!-- 이미지 -->
      <div v-if="imageExists" class="text-center">
        <img 
          :src="imageUrl" 
          :alt="`이미지 ${code}`"
          class="w-full max-w-[600px] h-auto"
          @load="onImageLoad"
          @error="onImageError"
        />
      </div>
      
      <!-- 출력 버튼 (g로 끝나는 파일에만 표시) -->
      <div v-if="isPrintFile && imageExists" class="mt-8 text-center pb-40">
        <button 
          @click="printImage"
          class="px-8 py-3 text-white font-semibold rounded-2xl"
          style="background-color: rgb(51, 51, 51);"
        >
          출력하기
        </button>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
// 라우트 파라미터 가져오기
const route = useRoute()
const code = route.params.code as string

// 이미지 URL 생성
const imageUrl = computed(() => `/${code}.png`)

// 이미지 존재 여부 확인
const imageExists = ref(true)
const imageLoaded = ref(false)

// g로 끝나는 파일인지 확인 (출력용)
const isPrintFile = computed(() => code.endsWith('g'))

// 이미지 로드 이벤트 핸들러
const onImageLoad = () => {
  imageLoaded.value = true
}

const onImageError = () => {
  imageExists.value = false
  imageLoaded.value = false
}

// 출력 기능
const printImage = () => {
  if (typeof window !== 'undefined') {
    window.print()
  }
}

// 페이지 메타 설정
useHead({
  title: `이미지 뷰어 - ${code}`,
  meta: [
    { name: 'description', content: `이미지 ${code}를 확인하세요` }
  ]
})

// SEO를 위한 동적 메타 태그
watch(() => route.params.code, (newCode) => {
  if (newCode) {
    useHead({
      title: `이미지 뷰어 - ${newCode}`,
      meta: [
        { name: 'description', content: `이미지 ${newCode}를 확인하세요` }
      ]
    })
  }
})
</script>

<style scoped>
/* 출력 시 스타일 */
@media print {
  .container {
    max-width: none !important;
    margin: 0 !important;
    padding: 0 !important;
  }
  
  .bg-gray-50 {
    background: white !important;
  }
  
  .shadow-lg {
    box-shadow: none !important;
  }
  
  .rounded-lg {
    border-radius: 0 !important;
  }
  
  /* 출력 버튼 숨기기 */
  button {
    display: none !important;
  }
  
  /* 이미지만 출력 */
  img {
    max-width: 100% !important;
    height: auto !important;
    page-break-inside: avoid;
  }
}
</style>
