<template>
  <div
    class="fileAttache"
    @drop="dropFile"
    @dragenter.prevent
    @dragover.prevent
  >
    <div class="top">
      <span @click="openFile">파일 첨부</span>
    </div>
    <div class="divPlaceholder">
      <p v-show="divFg">
        여기로 파일을 끌어놓으세요.
      </p>
    </div>
    <div ref="fileList" class="fileList" />
  </div>
</template>
<script>
export default {
  props: {
    files: { type: Array, default: () => [] } // 부모 컴포넌츠와 공유하는 FileList
  },
  data () {
    return {
      childFiles: [],
      divFg: true,
      arr_idx: 0
    }
  },
  watch: {
    childFiles (val) { // childFiles 데이터가 바뀔 때마다 함수 실행
      this.$emit('setFiles', val)

      // '여기로 파일을 끌어놓으세요.'를 표시할 flag 설정
      if (val.length > 0) {
        this.divFg = false
      } else {
        this.divFg = true
      }
    },
    files (val) {
      if (val.length === 0) {
        this.$refs.fileList.replaceChildren()
      }
    }
  },
  methods: {
    openFile () { // 파일 선택창 열기
      const input = document.createElement('input')
      input.type = 'file'
      input.click()
      input.onchange = (e) => { // 파일을 선택했을 경우
        const file = e.target.files[0]
        this.addFile(file)
      }
    },
    dropFile (event) { // 파일 drag & drop 이벤트 실행
      event.preventDefault()
      event.stopPropagation()

      this.addFile(event.dataTransfer.files[0])
    },
    async addFile (file) {
      if (file) {
        const url = await this.upload(file) // 서버에 파일 업로드
        const data = {
          arr_idx: this.arr_idx++,
          fileName: file.name,
          filePath: url
        }
        this.childFiles.push(data) // FileList 배열에 선택한 파일 데이터 추가
        this.addElement(data) // Element 요소 추가
      }
    },
    async upload (file) {
      const formData = new FormData()
      formData.append('uploadfile', file)
      const response = await this.$axios.post('http://localhost:8080/editor/upload/file', formData, {
        headers: {
          'Content-Type': 'multipart/form-data'
        }
      })
      // '/upload' 엔드포인트는 업로드된 파일의 URL 반환
      return response.data
    },
    addElement (data) { // 첨부파일 제목을 나타내는 Element 요소 추가
      const newP = document.createElement('p')
      newP.style.margin = '1px'
      newP.classList.add(data.arr_idx)
      newP.textContent = data.fileName
      this.$refs.fileList.appendChild(newP)

      const newI = document.createElement('i')
      newI.classList.add('el-icon-error')
      newI.addEventListener('click', () => this.delFile(data.arr_idx))
      newI.style.padding = '5px'
      newP.appendChild(newI)
    },
    delFile (idx) {
      this.$refs.fileList.getElementsByClassName(idx)[0].remove() // Element 요소 삭제
      const indexToRemove = this.childFiles.findIndex(item => item.arr_idx === idx) // childFiles 배열에서 arr_idx로 idx값을 가진 데이터 인덱스 반환
      this.childFiles.splice(indexToRemove, 1) // 반환된 인덱스를 통해 배열에서 내용 삭제
    }
  }
}
</script>
<style scoped>
.fileAttache {
  position: relative;
  border: 1px dashed #DCDFE6;
  margin: 5px 0px;
  padding-left: 20px;
}

.top {
  position: absolute;
  top: 2px;
  left: 0;
  width: 99%;
  padding: 12px 0 14px 20px;
  color: #017901;
}

.top>span {
  cursor: pointer;
}

.divPlaceholder {
  margin: 12px 0 14px;
  text-align: center;
  color: #DCDFE6;
  min-height: 25px;
}

.fileName {
  margin: 0 !important;
}
</style>
