<template>
  <div class="component-markdown">
    <div class="spin-group" v-if="!markedHTML">
      <i class="fa fa-spinner fa-pulse fa-2x fa-fw"></i>
      <span class="sr-only">Loading...</span>
    </div>
    <div class="markdown-body content" v-html="markedHTML"></div>
  </div>
</template>

<script>
import marked from "marked";
import { ComponentsMarkdownBase } from "@api/components.markdown";
export default {
  name: "markdown",
  props: {
    url: {
      type: String,
      required: false,
      default: "",
    },
    source: {
      type: String,
      required: false,
      default: "",
    },
    highlight: {
      type: Boolean,
      required: false,
      default: false,
    },
  },
  data() {
    return {
      getReadmePublicPath: "",
      markedHTML: "",
    };
  },
  mounted() {
    if (this.url) {
      this.initWithUrl();
    } else if (this.source) {
      this.initWithMd();
    } else {
      console.log("not mounted init");
    }
  },
  watch: {
    source(val) {
      if (val) {
        this.initWithMd();
      }
    },
  },
  methods: {
    handler(source) {
      if (source) {
        source = source.replace(/---\n([\s\S]*)---\n/, "");
      }
      return source;
    },
    // 使用 md 初始化
    initWithMd() {
      this.markedHTML = this.marked(this.handler(this.source));
    },
    // 使用 url 初始化
    async initWithUrl() {
      this.markedHTML = await this.getReadme(this.url);
    },
    // 从 url 加载原始数据
    async getReadme(url) {
      const data = await ComponentsMarkdownBase(url);
      console.log(data);
      return this.marked(data.data);
    },
    marked(data) {
      const renderer = new marked.Renderer();
      return marked(data, {
        renderer,
      });
    },
  },
};
</script>

<style lang="scss">
.component-markdown {
  .spin-group {
    height: 100px;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
  }
}
</style>
