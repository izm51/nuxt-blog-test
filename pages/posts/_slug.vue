<template>
  <article class="article">
    <div class="single">
      <h1 class="post-title">{{ post.fields.title }}</h1>
      <p class="post-created-at">{{ formatDate(post.sys.createdAt) }}</p>
      <div class="post-content line-numbers" v-html="$md.render(post.fields.content)"></div>
    </div>
  </article>
</template>

<script>
import client from '~/plugins/contentful'

export default {
  asyncData({ params, error, payload }) {
    if (payload) return { post: payload }
    return client
      .getEntries({
        content_type: 'post',
        'fields.slug': params.slug,
      })
      .then(entries => {
        return { post: entries.items[0] }
      })
      .catch(e => console.log(e))
  },
  head() {
    return {
      title: this.post.fields.title,
    }
  },
  mounted() {
    console.log(this.post)
    Prism.highlightAll()
  },
  methods: {
    formatDate(iso) {
      const date = new Date(iso)
      const yyyy = new String(date.getFullYear())
      const mm = new String(date.getMonth() + 1).padStart(2, "0")
      const dd = new String(date.getDate()).padStart(2, "0")
      return `${yyyy}.${mm}.${dd}`
    }
  }
}
</script>

<style lang="scss">
article.article {
  padding: 10px;
  .single {
    max-width: 900px;
    margin: 0 auto;
    padding: 15px;
    color: #222;
    border: 2px solid #444;
    border-radius: 10px;
    h1, h2, h3 {
      margin: 16px 0;
    }
    h1.post-title {
      font-size: 32px;
      text-decoration: underline;
    }
    .post-content {
      padding: 0 0 15px;
      position: relative;
      z-index: 1;
      > * {
        position: relative;
        z-index: 10; // h2のpadding部分より前面に来るように
      }
      .table-of-contents {
        background: #f3f3f3;
        border: 1px solid #ccc;
        padding: 5px 10px;
        width: fit-content;
        ul {
          padding-left: 24px;
        }
        &:before {
          content: '目次';
          font-weight: bold;
          font-size: 16px;
        }
      }
      h1 {
        font-size: 32px;
      }
      h2 {
        font-size: 24px;
        margin-top: -64px;
        padding: 80px 5px 0;
        position: relative;
        z-index: 1;
        &:before {
          content: '';
          height: 34px;
          width: 100%;
          background: #d6d6d6;
          position: absolute;
          top: 80px;
          left: 0;
          z-index: -1;
        }
      }
      p {
        margin: 16px 0;
        font-size: 16px;
      }
      img {
        max-width: 100%;
        border: 1px solid #000;
      }
    }
  }
}
</style>