<template>
  <div>
    <!-- Breadcrumb -->
    <Breadcrumb breadcrumb="Team" />

    <button
      @click="openAddTeamModal"
      class="px-4 py-2 font-medium tracking-wide text-white capitalize transition-colors duration-200 transform bg-indigo-600 rounded-md hover:bg-indigo-500 focus:outline-none focus:bg-indigo-500"
    >
      팀 추가
    </button>
    <ModalLayout
      v-if="isAddTeamModalOpen"
      title="팀 추가"
      @close="closeAddTeamModal"
    >
      <TeamAdd @form-submitted="handleTeamAddSubmit" />
    </ModalLayout>
    <!-- 검색바 -->
    <div>
      <div class="flex flex-col mt-3 sm:flex-row">
        <div class="flex">
          <!-- 표시할 항목 수 -->
          <div class="relative">
            <select
              v-model="size"
              @change="fetchPostData"
              class="block w-full h-full px-4 py-2 pr-8 leading-tight text-gray-700 bg-white border border-gray-400 rounded-l appearance-none focus:outline-none focus:bg-white focus:border-gray-500"
            >
              <option>10</option>
              <option>20</option>
              <option>30</option>
            </select>

            <div
              class="absolute inset-y-0 right-0 flex items-center px-2 text-gray-700 pointer-events-none"
            >
              <svg
                class="w-4 h-4 fill-current"
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 20 20"
              >
                <path
                  d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z"
                />
              </svg>
            </div>
          </div>
          <!-- 정렬 옵션 -->
          <div class="relative">
            <select
              v-model="postSortOption"
              @change="fetchPostData"
              class="block w-full h-full px-4 py-2 pr-8 leading-tight text-gray-700 bg-white border-t border-b border-r border-gray-400 rounded-r appearance-none sm:rounded-r-none sm:border-r-0 focus:outline-none focus:border-l focus:border-r focus:bg-white focus:border-gray-500"
            >
              <option value="">전체</option>
              <option value="OPEN">모집중</option>
              <option value="CLOSED">마감</option>
              <option value="IN_PROGRESS">진행중</option>
              <option value="COMPLETE">완료</option>
            </select>

            <div
              class="absolute inset-y-0 right-0 flex items-center px-2 text-gray-700 pointer-events-none"
            >
              <svg
                class="w-4 h-4 fill-current"
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 20 20"
              >
                <path
                  d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z"
                />
              </svg>
            </div>
          </div>
          <!-- 검색 옵션 -->
          <div class="relative">
            <select
              v-model="selectOption"
              class="block w-full h-full px-4 py-2 pr-8 leading-tight text-gray-700 bg-white border-t border-b border-r border-gray-400 rounded-r appearance-none sm:rounded-r-none sm:border-r-0 focus:outline-none focus:border-l focus:border-r focus:bg-white focus:border-gray-500"
            >
              <option value="">---</option>
              <option value="teamName">팀 명</option>
              <option value="teamIntroduce">소개</option>
            </select>

            <div
              class="absolute inset-y-0 right-0 flex items-center px-2 text-gray-700 pointer-events-none"
            >
              <svg
                class="w-4 h-4 fill-current"
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 20 20"
              >
                <path
                  d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z"
                />
              </svg>
            </div>
          </div>
        </div>
        <!-- 검색 창 -->
        <div class="relative block mt-2 sm:mt-0">
          <span class="absolute inset-y-0 left-0 flex items-center pl-2">
            <svg viewBox="0 0 24 24" class="w-4 h-4 text-gray-500 fill-current">
              <path
                d="M10 4a6 6 0 100 12 6 6 0 000-12zm-8 6a8 8 0 1114.32 4.906l5.387 5.387a1 1 0 01-1.414 1.414l-5.387-5.387A8 8 0 012 10z"
              />
            </svg>
          </span>

          <input
            v-model="searchQuery"
            @keydown.enter="fetchPostData"
            placeholder="검색어를 입력해주세요"
            class="block w-full py-2 pl-8 pr-6 text-sm text-gray-700 placeholder-gray-400 bg-white border border-b border-gray-400 rounded-l rounded-r appearance-none sm:rounded-l-none focus:bg-white focus:placeholder-gray-600 focus:text-gray-700 focus:outline-none"
          />
        </div>
      </div>
    </div>
    <!-- 테이블 -->
    <AppTable
      v-if="postData.content && postData.content.length > 0"
      :posts="postData.content"
      :current-page="page"
      :items-per-page="parseInt(size)"
      :total-pages="postData.totalPages"
      @set-page="setPage"
    />
    <div v-else class="text-center mt-4">검색 결과가 없습니다.</div>
  </div>
</template>

<script>
import Breadcrumb from "@/partials/AppBreadcrumb.vue";
import AppTable from "@/components/team/TeamTable.vue";
import ModalLayout from "@/components/ModalLayout.vue"; // ModalLayout 컴포넌트 import
import TeamAdd from "@/components/team/TeamAdd.vue"; // TeamAdd 컴포넌트 import
import axios from "@/api/index";

export default {
  name: "TeamView",
  components: {
    AppTable,
    Breadcrumb,
    ModalLayout, // 컴포넌트 등록
    TeamAdd, // 컴포넌트 등록
  },
  data() {
    return {
      postData: [], // 초기 데이터 정의
      postSortOption: "", // 기본 옵션
      page: 1, // 초기 페이지 번호 설정
      size: 10,
      searchQuery: "", // 추가된 데이터 속성
      selectOption: "",
      isAddTeamModalOpen: false,
      selectedTeam: null,
    };
  },
  mounted() {
    // 컴포넌트가 마운트 될 때 실행되는 라이프사이클 훅
    this.fetchPostData(); // 데이터 가져오는 메소드 호출
  },
  watch: {
    $route(to, from) {
      if (to.path !== from.path) {
        this.fetchPostData(); // 라우트가 변경될 때 데이터 가져오기
      }
    },
  },
  methods: {
    // 데이터를 가져오는 메소드
    fetchPostData() {
      // 기본 요청 파라미터
      const params = {
        // boardType: this.boardType,
        projectStatus: this.postSortOption,
        [this.selectOption]: this.searchQuery,
        page: this.page - 1, // 현재 페이지 번호 -1 (0 기반 인덱스)
        size: this.size,
      };

      console.log(params);

      axios
        .get("/team", { params })
        .then((response) => {
          console.log("응답 데이터:", response.data); // 응답 데이터 로그 추가
          this.postData = response.data; // 응답 데이터를 postData에 저장
        })
        .catch((error) => {
          console.error("데이터를 불러오는 중, 오류 발생 :", error); // 오류 처리
          // 오류 발생 시 postData 초기화 및 페이지 번호 초기화
          this.postData = { content: [] };
          this.page = 1;
        });
    },
    // 분류별로 데이터를 가져오는 메소드
    changeBoardType(type) {
      this.boardType = type;
      this.page = 1; // 페이지 초기화
      this.searchQuery = ""; // 검색 초기화
      this.fetchPostData();
    },
    // 페이징 시 값 변경 메소드
    setPage(page) {
      this.page = page; // 부모 컴포넌트에서 페이지 업데이트
      this.fetchPostData(); // 데이터 재 호출
    },
    openAddTeamModal() {
      this.isAddTeamModalOpen = true;
    },
    closeAddTeamModal() {
      this.isAddTeamModalOpen = false;
    },
    // 팀 추가 모달
    handleTeamAddSubmit(data) {
      // TeamAdd 컴포넌트에서 제출된 데이터 처리
      console.log("제출된 데이터:", data);

      // API 호출 또는 데이터 처리 로직 추가
      axios
        .post("/team", null, {
          params: {
            teamName: data.name,
            teamIntroduce: data.introduce,
            projectStatus: data.option,
          },
        })
        .then((response) => {
          console.log("응답 데이터:", response.data);
          this.fetchPostData(); // 데이터 재로드
        })
        .catch((error) => {
          console.error("데이터를 불러오는 중, 오류 발생:", error);
        });

      this.closeAddTeamModal(); // 모달 닫기
    },
    // 팀 선택 모달
    handleTeamSelected(team) {
      this.selectedTeam = team;
    },
  },
};
</script>
