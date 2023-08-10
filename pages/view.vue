<template>
  <div class="content container-wrap">
    <!-- 상단 타이틀 -->
    <div class="box-area">
      <div class="title-area">
        <h3>게시판</h3>
      </div>
      <div class="btn-area">
        <el-button type="primary" @click="move('./')">목록</el-button>
        <el-button type="primary" @click="move('./create')">글쓰기</el-button>
        <el-button type="primary" @click="move(`./update?idx=${$route.query.idx}`)">수정</el-button>
        <el-button type="primary" @click="move('./')">삭제</el-button>
      </div>
    </div>
    <!-- // 상단 타이틀 -->

    <!-- 컨텐츠 영역 -->
    <div class="table-responsive">
      <table class="table table-sm border table-horizontal table-fixed table-view">
        <colgroup>
          <col width="15%">
          <col width="35%">
          <col width="15%">
          <col width="35%">
        </colgroup>
        <tbody>
          <tr>
            <th scope="row">제목</th>
            <td colspan="3">{{ boardData.title }}</td>
          </tr>
          <tr>
            <th scope="row">작성자</th>
            <td>{{ boardData.user_id }}</td>
            <th scope="row">작성일</th>
            <td>{{ boardData.cre_dt }}</td>
          </tr>
          <tr>
            <td colspan="4" class="txt">
              <div class="content" v-html="boardData.content" />
            </td>
          </tr>
          <tr>
            <th scope="row">첨부파일</th>
            <td colspan="3">
              <div v-for="(file, index) in boardData.files" :key="index">
                <a @click="downFile(file.filePath)">
                  <i class="el-icon-document" /><span>{{ file.fileName }}</span>
                </a><br>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <!-- 컨텐츠 영역 -->

    <!--이전/다음-->
    <ul class="article-nav">
      <li>
        <span class="txt">다음글<i class="ico" /></span>
        <span v-if="afterData == null" class="ellipsis">다음글이 없습니다.</span>
        <a v-else :href="'./view?idx=' + afterData.idx">
          {{ afterData.title }}
        </a>
      </li>
      <li>
        <span class="txt">이전글<i class="ico" /></span>
        <span v-if="beforeData == null" class="ellipsis">이전글이 없습니다.</span>
        <a v-else :href="'./view?idx=' + beforeData.idx">
          {{ beforeData.title }}
        </a>
      </li>
    </ul>
    <!--//이전/다음-->
  </div>

</template>
<script>
import boardList from '~/static/data/board.json'

export default {
  name: 'Board',
  layout: 'MainLayout',
  data () {
    return {
      beforeData: {},
      boardData: {
        idx: '',
        user_id: '',
        title: '',
        content: '',
        cre_dt: '',
        udt_dt: '',
        files: []
      },
      boards: boardList,
      afterData: {}
    }
  },
  beforeMount () {
  },
  mounted () {
    this.init()
  },

  methods: {
    init () {
      // this.$axios.get(`http://localhost:8080/editor/get/board/${this.$route.query.idx}`)
      //   .then((res) => {
      //     this.beforeData = res.data[0]
      //     this.boardData = res.data[1]
      //     this.afterData = res.data[2]
      //   })
      //   .catch((error) => {
      //     console.log('error : ' + error)
      //   })
      this.boardData = this.getContentByIdx(this.$route.query.idx)
      this.beforeData = this.boards[this.boards.indexOf(this.boardData) - 1]
      this.afterData = this.boards[this.boards.indexOf(this.boardData) + 1]
    },
    getContentByIdx(targetIdx) {
      return this.boards.find(obj => obj.idx === Number(targetIdx));
    },
    move (data) {
      this.$router.push(data)
    },
    delBoard () {
      this.$axios.get(`http://localhost:8080/editor/del/board/${this.$route.query.idx}`)
        .then((res) => {
          this.$router.push('./list')
        })
        .catch((error) => {
          console.log('error : ' + error)
        })
    },

    // path: '/upload/video_1689819382493.mp4'
    downFile (path) {
      const data = {
        filePath: path
      }
      this.$axios.post('http://localhost:8080/editor/download/file', data, {
        responseType: 'blob' // 다운로드 받을 파일의 형식이 바이너리임을 명시
      }).then((response) => {
        // 다운로드 요청이 성공하면 파일 다운로드 시작
        // url = http://localhost:3000/f656c367-0747-41f2-b2be-d90cd5ff59da
        const url = window.URL.createObjectURL(response.data)
        // 파일을 다운로드하기 위한 링크 생성
        const link = document.createElement('a')
        link.href = url
        // 링크의 download 속성을 설정하여 파일명 지정
        link.setAttribute('download', path.split('/').reverse()[0])
        // 링크를 클릭하여 파일 다운로드 시작
        link.click()
      }).catch(() => {
        this.alert(this.$t('삭제된 파일입니다.'))
      })
    }
  }
}
</script>
