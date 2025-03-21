<template>
  <div class="mt-4">
    <div class="mt-6">
      <div class="my-6 overflow-hidden bg-white rounded-md shadow">
        <table class="w-full text-left border-collapse">
          <!-- 제목 -->
          <thead class="border-b">
            <tr>
              <th
                class="px-5 py-3 text-sm font-medium text-gray-100 uppercase bg-indigo-800"
              >
                번호
              </th>
              <th
                class="px-5 py-3 text-sm font-medium text-gray-100 uppercase bg-indigo-800"
              >
                제목
              </th>
              <th
                class="px-5 py-3 text-sm font-medium text-gray-100 uppercase bg-indigo-800"
              >
                글쓴이
              </th>
              <th
                class="px-5 py-3 text-sm font-medium text-gray-100 uppercase bg-indigo-800"
              >
                작성일
              </th>
            </tr>
          </thead>
          <!-- 내용 -->
          <tbody>
            <tr
              v-for="(post, i) in paginatedPosts"
              :key="i"
              @click="ditailePage(post.postNo)"
              class="hover:bg-gray-200 cursor-pointer"
            >
              <td class="px-6 py-4 text-lg text-gray-500 border-b">
                {{ post.postNo }}
              </td>
              <td class="px-6 py-4 text-gray-700 border-b">
                {{ post.title }}
              </td>
              <td class="px-6 py-4 text-gray-500 border-b">
                {{ post.userName }}
              </td>
              <td class="px-6 py-4 text-gray-500 border-b">
                {{ new Date(post.createdAt).toLocaleDateString() }}
              </td>
            </tr>
          </tbody>
        </table>
        <!-- 페이징 버튼 -->
        <div
          class="flex flex-col items-center px-5 py-5 bg-white border-t xs:flex-row xs:justify-between"
        >
          <div class="flex mr-4 rounded">
            <a
              href="#"
              @click="prevPage(post.no)"
              class="px-3 py-2 ml-0 leading-tight text-indigo-700 bg-white border border-r-0 border-gray-200 rounded-l hover:bg-indigo-500 hover:text-white"
              ><span>Previous</span></a
            >
            <a
              href="#"
              v-for="page in totalPages"
              :key="page"
              :class="
                page === currentPage
                  ? 'bg-indigo-500 text-white'
                  : 'text-indigo-700'
              "
              @click="setPage(page)"
              class="px-3 py-2 leading-tight bg-white border border-r-0 border-gray-200 hover:bg-indigo-500 hover:text-white"
              ><span>{{ page }}</span></a
            >
            <a
              href="#"
              @click="nextPage"
              class="px-3 py-2 leading-tight text-indigo-700 bg-white border border-gray-200 rounded-r hover:bg-indigo-500 hover:text-white"
              ><span>Next</span></a
            >
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "AppTable",
  props: {
    // 부모로 부터 받아오는 값들
    // 기본 데이터 배열
    posts: {
      type: Array,
      default: () => [], // 기본값을 빈 배열로 설정
      required: true,
    },
    // 현재 페이지 위치 (기본 1)
    currentPage: {
      type: Number,
      default: 1,
    },
    // 한 페이지에 나타낼 항목 수 (기본 10)
    itemsPerPage: {
      type: Number,
      default: 10,
    },
    // 총 페이지 수
    totalPages: {
      type: Number,
      required: true,
    },
  },
  emits: ["set-page"], // 이벤트 정의 (부모에게 값을 전달)
  // data() {
  //   return {
  //     // 컴포넌트 내부 변수
  //   };
  // },
  computed: {
    // 페이징 처리된 페이지를 반환
    paginatedPosts() {
      return this.posts; // posts 배열 직접 반환
    },
  },
  methods: {
    // 상세 글 보기
    ditailePage(no) {
      this.$router.push(`/post/${no}`);
    },
    // 페이징 버튼 메소드
    setPage(page) {
      this.$emit("set-page", page);
    },
    // 페이징 뒤로 버튼
    prevPage() {
      if (this.currentPage > 1) {
        this.$emit("set-page", this.currentPage - 1);
      }
    },
    // 페이징 앞으로 버튼
    nextPage() {
      if (this.currentPage < this.totalPages) {
        this.$emit("set-page", this.currentPage + 1);
      }
    },
  },
};
</script>
