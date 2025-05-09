<script lang="ts">
import { Vue, Component } from 'vue-facing-decorator'
import formats from '@/data/formats'
import defaults from '@/data/defaults'
import {Icon} from '@iconify/vue'
import { marked } from 'marked'

@Component({components: {Icon}})
export default class App extends Vue {
  formats = formats
  text = defaults.text
  html = ''

  add(s: string, double: boolean = true) {
    const textAreaEl = document.getElementById('textarea0') as HTMLTextAreaElement
    let start = textAreaEl.selectionStart
    let end = textAreaEl.selectionEnd
    const texts = this.text.split('')
    if (double) {
      texts[end - 1] += s;
    }
    texts[start - 1] += s;
    this.text = texts.join('');
    start += s.length;
    end += s.length;
    textAreaEl.selectionStart = start;
    textAreaEl.selectionEnd = end;
  }

  rend() {
    const md = this.text.replace(/--(.*?)--/g, (match, inner) => {
      return `<u>${inner}</u>`
    })
    this.html = marked.parse(md) as string
  }

  mounted() {
    this.rend()
  }
}
</script>

<template>
  <div class="left">
    <div class="edit-pane">
      <div class="pane-button" v-for="e of formats" :key="'format.' + e.name" @click="add(e.style, e.double)">
        <Icon class="pane-icon" :icon="e.icon" />
      </div>
    </div>
    <textarea class="edit-area" v-model="text" @change="rend" @keydown="rend" @keyup="rend" @click="rend" :id="'textarea0'"></textarea>
  </div>
  <div class="right">
    <div class="markdown-content rendered" v-html="html"></div>
  </div>
</template>

<style lang="scss">
@import '@/style/markdown';
@import '@/style/root';
@import '@/style/fonts';

* {
  background: transparent;
}

body {
  background: var(--color-background);
}

#app {
  min-height: 95vh;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  width: 100%;
  max-width: 1280px;
  margin: 0 auto;
  gap: 16px;
}

.left {
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: start;
  width: 100%;
  height: 90vh;
  gap: 8px;
  max-width: min(644px, 49vw, 100%);

  .edit-pane {
    height: 36px;
    padding: 4px;
    color: var(--color-background);
    border-radius: 8px;
    display: flex;
    flex-direction: row;
    justify-content: start;
    align-items: center;
    width: fit-content;
    box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.2), 0 3px 10px 0 rgba(0, 0, 0, 0.19);
    gap: 8px;

    .pane-button {
      display: flex;
      box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.2);
      transition: all 0.5s ease;
      color: var(--color-text);
      width: 32px;
      height: 32px;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      cursor: pointer;
      border: 1.5px solid var(--color-special);
      border-radius: 8px;

      .pane-icon {
        color: var(--color-text);
        width: 28px;
        height: 28px;
        object-fit: contain;
        overflow: hidden;
        display: inline-block;
      }

      &:hover {
        transform: translateY(-4px);
        box-shadow: 0 1px 6px 0 rgba(0, 0, 0, 0.2);
        border: 1.5px solid var(--color-hover);
      }
    }
  }

  .edit-area {
    resize: none;
    height: calc(95% - 48px);
    min-height: calc(95% - 40px);
    width: 100%;
    font-family: var(--font-family-mono), monospace;
    color: var(--color-text);
    border-radius: 4px;
    border: 1px solid var(--color-highlight);
    transition: all 0.5s ease;
    box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.2), 0 3px 10px 0 rgba(0, 0, 0, 0.19);

    &:active {
      border: 1px solid var(--color-hover);
      outline: none;
    }
  }
}

.right {
  flex: 1;
  height: 90vh;
  max-width: min(644px, 49vw, 100%);

  .rendered {
    width: 100%;
    height: 95%;
    min-height: 95%;
    border: 1px solid var(--color-special);
    border-radius: 4px;
    box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.2), 0 3px 10px 0 rgba(0, 0, 0, 0.19);
    padding: 8px;
    max-width: min(644px, 49vw, 100%);
  }
}
</style>
