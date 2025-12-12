<template>
  <div v-if="editor" class="container">
    <div class="control-group">
      <div class="button-group">
        <button @click="addImage">Adicionar imagem</button>
      </div>
    </div>

    <!-- Editor -->
    <editor-content 
      class="tiptap"
      :editor="editor"
    />
  </div>
</template>

<script>
import Document from '@tiptap/extension-document'
import Image from '@tiptap/extension-image'
import Paragraph from '@tiptap/extension-paragraph'
import Text from '@tiptap/extension-text'
import { Dropcursor } from '@tiptap/extension-dropcursor'
import { Editor, EditorContent } from '@tiptap/vue-3'

export default {
  components: { EditorContent },

  props: {
    modelValue: {
      type: String,
      default: "",
    }
  },

  data() {
    return {
      editor: null,
    }
  },

  methods: {
    addImage() {
      const url = window.prompt("URL da imagem:")
      if (url) {
        this.editor.chain().focus().setImage({ src: url }).run()
      }
    },
  },

  mounted() {
    this.editor = new Editor({
      content: this.modelValue || `
        <p>Editor TipTap com Resize de Imagem funcionando!</p>
        <img src="https://placehold.co/600x400" />
      `,

      extensions: [
        Document,
        Paragraph,
        Text,
        Image.configure({
          resize: {
            enabled: true,
            alwaysPreserveAspectRatio: true,
          },
        }),
        Dropcursor,
      ],

      onUpdate: () => {
        const html = this.editor.getHTML()
        this.$emit("update:modelValue", html)   // <- devolve para o WeWeb
      }
    })
  },

  beforeUnmount() {
    if (this.editor) {
      this.editor.destroy()
    }
  },
}
</script>

<style>
/* Basic editor styles */
.tiptap :first-child {
  margin-top: 0;
}

.tiptap img {
  display: block;
}

/* Resize handles */
.tiptap [data-resize-handle] {
  position: absolute;
  background: rgba(0, 0, 0, 0.5);
  border: 1px solid rgba(255, 255, 255, 0.8);
  border-radius: 2px;
  z-index: 10;
}
.tiptap [data-resize-handle]:hover {
  background: rgba(0, 0, 0, 0.8);
}

/* corner handles */
.tiptap [data-resize-handle="top-left"],
.tiptap [data-resize-handle="top-right"],
.tiptap [data-resize-handle="bottom-left"],
.tiptap [data-resize-handle="bottom-right"] {
  width: 8px;
  height: 8px;
}

.tiptap [data-resize-handle="top-left"] { top: -4px; left: -4px; cursor: nwse-resize; }
.tiptap [data-resize-handle="top-right"] { top: -4px; right: -4px; cursor: nesw-resize; }
.tiptap [data-resize-handle="bottom-left"] { bottom: -4px; left: -4px; cursor: nesw-resize; }
.tiptap [data-resize-handle="bottom-right"] { bottom: -4px; right: -4px; cursor: nwse-resize; }

/* edge handles */
.tiptap [data-resize-handle="top"],
.tiptap [data-resize-handle="bottom"] {
  height: 6px;
  left: 8px;
  right: 8px;
}
.tiptap [data-resize-handle="top"] { top: -3px; cursor: ns-resize; }
.tiptap [data-resize-handle="bottom"] { bottom: -3px; cursor: ns-resize; }

.tiptap [data-resize-handle="left"],
.tiptap [data-resize-handle="right"] {
  width: 6px;
  top: 8px;
  bottom: 8px;
}
.tiptap [data-resize-handle="left"] { left: -3px; cursor: ew-resize; }
.tiptap [data-resize-handle="right"] { right: -3px; cursor: ew-resize; }

.tiptap [data-resize-state="true"] [data-resize-wrapper] {
  outline: 1px solid rgba(0, 0, 0, 0.25);
  border-radius: 2px;
}
</style>
