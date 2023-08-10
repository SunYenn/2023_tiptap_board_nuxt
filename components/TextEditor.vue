<template>
  <div class="editor">
    <!-- Menu bar -->
    <div class="editor__menubar" :editor="editor">
      <div class="menubar">
        <button class="menubar__button" @click="setBold">
          <img class="icon" src="@/static/icons/bold.svg" />
        </button>
        <button class="menubar__button" @click="setItalic">
          <img class="icon" src="@/static/icons/italic.svg" />
        </button>
        <button class="menubar__button" @click="setUnderline">
          <img class="icon" src="@/static/icons/underline.svg" />
        </button>
        <button class="menubar__button" @click="setImage">
          <img class="icon" src="@/static/icons/image.svg" />
        </button>
        <button class="menubar__button" @click="addVideo">
          <img class="icon" src="@/static/icons/youtube.svg" />
        </button>
      </div>
    </div>
    <!-- Editor content -->
    <div
      ref="editorContent"
      class="editor__content"
      :editor="editor"
      @update="handleUpdate"
      @click="editorFocus"
      @drop="dropFile"
    />
  </div>
</template>
<script>
import { Editor, Node } from '@tiptap/core'
import StarterKit from '@tiptap/starter-kit'
import Underline from '@tiptap/extension-underline'
import Image from '@tiptap/extension-image'
import Link from '@tiptap/extension-link'

const Video = Node.create({
  name: 'video',
  group: 'block',
  draggable: true,

  addAttributes () {
    return {
      src: { default: null }
    }
  },

  parseHTML () {
    return [
      {
        tag: 'iframe'
      }
    ]
  },

  renderHTML ({ HTMLAttributes }) {
    return ['iframe', HTMLAttributes]
  }
})

export default {
  props: {
    html: {
      type: String,
      default: ''
    }
  },

  data () {
    return {
      editor: null
    }
  },

  mounted () {
    this.editor = new Editor({
      extensions: [
        StarterKit,
        Underline,
        Image,
        Link,
        Video
      ],
      content: this.html,
      onUpdate: ({ editor }) => {
        this.handleUpdate(editor.getHTML())
      },
      // editorContent 요소 내부에 객체 생성
      element: this.$refs.editorContent
    })
    this.editor.setOptions({
      editable: true,
      dragInline: {}
    })
  },

  watch: {
    html (val) {
      this.editor.commands.setContent(val)
    }
  },

  methods: {
    editorFocus () {
      this.editor.chain().focus()
    },

    setBold () {
      this.editor.chain().focus().toggleBold().run()
    },

    setItalic () {
      this.editor.chain().focus().toggleItalic().run()
    },

    setUnderline () {
      this.editor.chain().focus().toggleUnderline().run()
    },

    // 업데이트 내용을 상위컴포넌트(create.vue)에 전달
    handleUpdate (html) {
      this.$emit('input', html)
    },

    // editor 내용 삭제
    reset () {
      if (this.editor) {
        this.editor.destroy()
      }
    },

    // 이미지 첨부
    setImage () {
      const input = document.createElement('input')
      input.type = 'file'
      input.accept = 'image/*'
      input.onchange = async () => {
        const file = input.files[0]
        if (file) {
          const url = await this.upload(file) // 업로드 된 파일 url /upload/33_1691647680314.jpg
          this.showImage(url)
        }
      }
      input.click()
    },

    // 로컬에 저장된 이미지를 에디터에 보여주기
    showImage (url) {
      this.editor.chain().focus().setImage({ src: 'http://localhost:8080' + url }).run()
    },

    // 동영상 첨부
    addVideo () {
      const input = document.createElement('input')
      input.type = 'file'
      input.accept = 'video/*'
      input.onchange = async () => {
        const file = input.files[0]
        if (file) {
          const url = await this.upload(file) 
          this.showVideo(url)
        }
      }
      input.click()
    },

    // 첨부한 영상을 iframe 형태로 에디터에 보여주기
    showVideo (url) {
      this.editor.chain().focus().insertContent(`<iframe src="http://localhost:8080${url}"></iframe>`).run()
    },

    // 이미지 드롭 이벤트
    async dropFile (event) {
      event.preventDefault()
      event.stopPropagation()

      const file = event.dataTransfer.files[0]
      const type = file.name.split('.').reverse()[0]

      const imgExtensions = ['jpg', 'gif', 'png', 'jpeg']
      const videoExtensions = ['mp4', 'm4v', 'avi', 'mpg', 'mpeg']
      const allExtensions = imgExtensions.concat(videoExtensions)

      if (!allExtensions.includes(type)) {
        console.log('이미지나 영상 형식만 올릴 수 있습니다.')
      } else {
        const url = await this.upload(file) // /upload/33_1691647680314.jpg
        if (videoExtensions.includes(type)) {
          this.showVideo(url)
        } else {
          this.showImage(url)
        }
      }
    },

    // 파일 업로드
    async upload (file) {
      // const formData = new FormData()
      // formData.append('uploadfile', file)
      // const response = await this.$axios.post('http://localhost:8080/editor/upload/file', formData, {
      //   headers: {
      //     'Content-Type': 'multipart/form-data'
      //   }
      // })
      // // '/upload' 엔드포인트는 업로드된 파일의 URL 반환
      // return response.data
    }

  }
}
</script>

<style scoped>
.editor{
  height:400px;
  margin:0 auto;
  border:1px solid #C0C4CC;
}
.menubar{
  height: 40px;
  background-color: rgb(241, 241, 239);
  display: flex;
  align-items: center;
  padding: 0 10px;
}
.editor__content{
  width:100%;
  height:85%;
  padding:7px;
  overflow: auto;
}

.editor__content > div {
  padding: 7px;
}
.editor__content img{
  width:300px;
}
.editor__content p{
  margin: 0;
}
.editor__content p > br.ProseMirror-trailingBreak{
  display: block;
  height: 100vh;
}
.editor__content p > strong{
  font-weight: bold;
}
.menubar__button{
  border: none;
  border-radius: 5px;
  height:25px;
  display: flex;
  align-items: center;
}
.menubar__button:hover{
  background-color: rgb(225, 226, 226);
  cursor: pointer;
}
.menubar__button > .icon{
  width:20px;
  height:20px;
}

.editor__menubar .menubar .menubar__button {
  margin-right: 5px;
}

</style>
