<template>
  <div class="content container-wrap"  ref="wrap">

    <!-- 상단 타이틀 -->
    <div class="box-area">
      <div class="title-area">
        <h3>게시판</h3>
      </div>
      <div class="btn-area">
        <el-select v-model="paging.page_size" style="width: 130px;" @change="init">
          <el-option
            v-for="option in [5, 7, 10]"
            :key="option"
            :label="option + '개씩 보기'"
            :value="option"
            align="center"
          />
        </el-select>
        <el-button type="primary" @click="createBoard">
          글쓰기
        </el-button>
      </div>
    </div>
    <!-- // 상단 타이틀 -->

    <!-- 컨텐츠 영역 -->
    <div>
      <el-table :data="boardData" @row-click="moveBoardView" :height="height">
        <el-table-column prop="rnum" label="글번호" width="80" align="center" />
        <el-table-column prop="title" label="제목" />
        <el-table-column prop="user_id" label="작성자" width="150" align="center" />
        <el-table-column prop="cre_dt" label="작성일" width="200" align="center" sortable />
        <el-table-column prop="udt_dt" label="수정일" width="200" align="center" sortable />
      </el-table>
    </div>
    <!-- // 컨텐츠 영역 -->

    <!-- 페이징 -->
    <el-pagination
      layout="prev, pager, next"
      :current-page="paging.current_page"
      :total="paging.total_page"
      @current-change="pageChange"
    />
    <!-- // 페이징 -->

  </div>
</template>
<script>
import boards from '~/static/data/board.json'

export default {
  name: 'Board',
  layout: 'MainLayout',
  data () {
    return {
      height: 0,
      search_category: [
        {
          label: '제목',
          value: 'title'
        }, {
          label: '작성자',
          value: 'user_id'
        }, {
          label: '제목 + 작성자',
          value: 'multi'
        }
      ],
      paging: {
        page_size: 10,
        current_page: 1,
        total_page: 0,
      },
      boardData: []
    }
  },
  beforeMount() {
  },
  async mounted () {
    this.height = await this.$refs.wrap.clientHeight - 167
    this.init()
  },
  methods: {
    init () {
      this.boardData = boards
      this.paging.total_page = (~~(boards.length / this.paging.page_size) + 1) * 10
    },
    moveBoardView (data) {
      this.$router.push(`./view?idx=${data.idx}`)
    },
    createBoard () {
      this.$router.push('./create')
    },
    pageChange (page) {
      this.paging.current_page = page
      this.init()
    },
    reset () {
      this.paging = {
        page_size: 10,
        current_page: 1,
        total_page: 10,
        search_category: '검색 조건',
        search_data: '',
        cre_dt_fg: false,
        udt_dt_fg: false
      }
      this.init()
    }
  }
}
</script>
