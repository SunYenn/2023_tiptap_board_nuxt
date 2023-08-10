<template>
  <div class="content container-wrap">

    <!-- 상단타이틀 -->
    <div class="box-area">
      <div class="title-area">
        <h2>게시판</h2>
      </div>
      <div class="btn-area">
        <el-button type="primary" @click="movelist">
          목록
        </el-button>
        <el-button type="primary" @click="reset">
          초기화
        </el-button>
        <el-button type="primary" @click="submit">
          저장
        </el-button>
      </div>
    </div>
    <!-- // 상단타이틀 -->

    <!-- 컨텐츠 영역 -->
    <div class="table-responsive">
      <div>
        <el-input v-model="create.title" :html="create.content" placeholder="제목을 입력해주세요" />
      </div>
      <!-- 파일 첨부 -->
      <FileAttache :files="create.files" @setFiles="setFiles"/>
      <!-- 내용 입력 (Tiptap Editor)-->
      <TextEditor ref="childEdior" @input="setContent"/>
    </div>
    <!-- // 컨텐츠 영역 -->

  </div>
</template>
<script>
import TextEditor from '~/components/TextEditor.vue'
import FileAttache from '~/components/FileAttache.vue'

export default {
  name: 'Board',
  layout: 'MainLayout',
  components: {
    TextEditor,
    FileAttache
  },
  data () {
    return {
      create: {
        title: '',
        content: '',
        files: []
      }
    }
  },
  methods: {
    movelist () {
      this.$router.push('./')
    },
    reset () {
      this.create.title = ''
      this.$refs.childEdior.reset() // 자식 컴포넌츠인 TextEditor의 reset 함수 호출
    },
    setContent (html) { // $emit으로 받아 온 TextEditor에서 작성한 내용 넣어주는 함수
      this.create.content = html
    },
    submit () {
      // this.$axios.post('http://localhost:8080/editor/cre/board', this.create)
      //   .then((res) => { // 글 저장 후 res.data에 idx값을 반환 받는다.
      //     this.$router.push({
      //       path: `./view?idx=${res.data}`
      //     })
      //   })
      //   .catch((error) => {
      //     console.log('error : ' + error)
      //   })
      this.$router.push('./')
    },
    setFiles (val) { // $emit으로 받아 온 FileAttache에서 선택한 파일명들 넣어주는 함수
      this.create.files = val
    }
  }
}
</script>
