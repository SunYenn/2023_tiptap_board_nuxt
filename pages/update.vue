<template>
  <div class="content container-wrap">

    <!-- 상단타이틀 -->
    <div class="box-area">
      <div class="title-area">
        <h2>게시판</h2>
      </div>
      <div class="btn-area">
        <el-button type="primary" @click="moveview">취소</el-button>
        <el-button type="primary" @click="reset">초기화</el-button>
        <el-button type="primary" @click="submit">저장</el-button>
      </div>
    </div>
    <!-- // 상단타이틀 -->

    <!-- 컨텐츠 영역 -->
    <div class="table-responsive">
      <div>
        <el-input v-model="update.title" placeholder="제목을 입력해주세요" />
      </div>
      <!-- 파일 첨부 -->
      <FileAttache :files="update.files" @setFiles="setFiles"/>
      <!-- 내용 입력 (Tiptap Editor)-->
      <TextEditor ref="childEdior" :html="update.content" @input="setContent"/>
    </div>
    <!-- // 컨텐츠 영역 -->

  </div>

</template>
<script>
import TextEditor from '~/components/TextEditor.vue'
import FileAttache from '~/components/FileAttache.vue'
import boardList from '~/static/data/board.json'

export default {
  name: 'Board',
  layout: 'MainLayout',
  components: {
    TextEditor,
    FileAttache
  },
  data () {
    return {
      update: {
        user_id: '',
        title: '',
        content: '',
        cre_dt: '',
        files: []
      },
      boards: boardList
    }
  },
  mounted () {
    this.init()
  },

  methods: {
    init () {
      // this.$axios.get(`http://localhost:8080/editor/get/board/${this.$route.query.idx}`)
      //   .then((res) => {
      //     this.update = res.data[1]
      //   })
      //   .catch((error) => {
      //     console.log('error : ' + error)
      //   })
      this.update = this.getContentByIdx(this.$route.query.idx)
    },
    getContentByIdx(targetIdx) {
      return this.boards.find(obj => obj.idx === Number(targetIdx));
    },
    moveview () {
      this.$router.push(`./view?idx=${this.$route.query.idx}`)
    },
    reset () {
      this.update.title = ''
      this.$refs.childEdior.reset() // 자식 컴포넌츠인 TextEditor의 함수 호출
    },
    setContent (html) {
      this.update.content = html
    },
    submit () {
      // this.$axios.post('http://localhost:8080/editor/udt/board', this.update)
      //   .then((res) => {
      //     this.$router.push({
      //       path: `./view?idx=${res.data}` // Replace with the actual path to the target page
      //     })
      //   })
      //   .catch((error) => {
      //     console.log('error : ' + error)
      //   })
      this.$router.push('./')
    },
    setFiles (val) {
      this.update.files = val
    }
  }
}
</script>
