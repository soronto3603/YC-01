<template>
  <div class="min-h-screen bg-gray-50 py-8">
    <div class="container mx-auto px-4">

      <!-- 이미지 컨테이너 -->
      <div class="bg-white rounded-lg shadow-lg overflow-hidden">
        <div class="p-6">
          <div v-if="imageExists" class="text-center">
            <img 
              :src="imageUrl" 
              :alt="`이미지 ${code}`"
              class="max-w-full h-auto mx-auto rounded-lg shadow-md"
              @load="onImageLoad"
              @error="onImageError"
            />
          </div>
          
          <div v-else class="text-center py-16">
            <div class="w-16 h-16 bg-red-100 rounded-full flex items-center justify-center mx-auto mb-4">
              <svg class="w-8 h-8 text-red-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-2.5L13.732 4c-.77-.833-1.964-.833-2.732 0L3.732 16.5c-.77.833.192 2.5 1.732 2.5z"></path>
              </svg>
            </div>
            <h3 class="text-xl font-semibold text-gray-900 mb-2">이미지를 찾을 수 없습니다</h3>
            <p class="text-gray-600 mb-4">코드 "{{ code }}"에 해당하는 이미지가 존재하지 않습니다.</p>
            <button 
              @click="$router.push('/')"
              class="bg-blue-600 hover:bg-blue-700 text-white font-semibold py-2 px-4 rounded-lg transition duration-200"
            >
              홈으로 돌아가기
            </button>
          </div>
        </div>
      </div>

      <!-- 출력 버튼 (g로 끝나는 파일에만 표시) -->
      <div v-if="isPrintFile && imageExists" class="mt-8 text-center">
        <button 
          @click="printImage"
          class="bg-green-600 hover:bg-green-700 text-white font-semibold py-3 px-8 rounded-lg shadow-lg transition duration-200 flex items-center mx-auto"
        >
          <svg class="w-5 h-5 mr-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 17h2a2 2 0 002-2v-4a2 2 0 00-2-2H5a2 2 0 00-2 2v4a2 2 0 002 2h2m2 4h6a2 2 0 002-2v-4a2 2 0 00-2-2H9a2 2 0 00-2 2v4a2 2 0 002 2zm8-12V5a2 2 0 00-2-2H9a2 2 0 00-2 2v4h10z"></path>
          </svg>
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
