<template>
  <layout>
    <div class="content">
      <div class="article-form-box">
        <div class="article-form-header">
          <div class="article-form-title">
            <h2>記事作成</h2>
          </div>
          <el-button
            type="primary"
            plain
            round
            style="margin-left: auto; margin-right: 2em"
            @click="submit"
          >
            投稿する
          </el-button>
        </div>
        <form>
          <table>
            <tr>
              <th>タイトル</th>
              <td>
                <i class="el-icon-postcard" />
              </td>
              <td>
                <el-input
                  placeholder="Please input"
                  v-model="form.title"
                  style="width: 25em;"
                />
                <a class="error_msg">{{ error.title }}</a>
              </td>
            </tr>
          </table>
          <div class="flex flex-column">
            <a class="error-content">{{ error.content }}</a>
            <mavon-editor
              :language="'ja'"
              v-model="form.content"
              @imgAdd="$imgAdd"
              :toolbars="toolbars"
              ref="me"
              style="border: solid 0.2em #afeeee; margin: 1.2em 1.5em;"
            />
          </div>
        </form>
      </div>
    </div>
  </layout>
</template>

<script>
import Layout from "@/Pages/Base/Layout";

export default {
  components: {
    Layout,
  },
  data() {
    return {
      form: {
        title: "",
        content: "",
      },

      error: {
        title: "",
        content: "",
      },

      toolbars: {
        bold: true,
        italic: true,
        header: true,
        underline: true,
        strikethrough: true,
        mark: true,
        superscript: true,
        subscript: true,
        quote: true,
        ol: true,
        ul: true,
        link: true,
        imagelink: true,
        code: true,
        table: true,
        help: true,
        alignleft: true,
        aligncenter: true,
        alignright: true,
        subfield: true,
        preview: true,
        // false
        undo: false,
        redo: false,
        fullscreen: false,
        readmodel: false,
        htmlcode: false,
        trash: false,
        save: false,
        navigation: false,
      }
    };
  },
  methods: {
    submit: function () {
      axios
        .post(this.$route("article.store"), this.form)
        .then((res) => this.sentNotify())
        .catch(
          function (res) {
            let err = res.response.data.errors;

            //TODO:あんまいい書き方じゃない気がする
            if (err.title) {
              this.error.title = err.title[0];
            } else {
              this.error.title = "";
            }
            if (err.content) {
              this.error.content = err.content[0];
            } else {
              this.error.content = "";
            }

            this.errorNotify();
          }.bind(this)
        );
    },
    sentNotify: function () {
      this.$notify.success({
        title: "保存しました",
        message: "トップに戻ります",
        offset: 150,
        duration: 10000,
        showClose: false,
      });
      setTimeout(this.toTop, 2000);
    },
    errorNotify: function () {
      this.$notify.error({
        title: "エラー",
        message: "入力値に誤りがあります。",
        offset: 150,
        duration: 10000,
        showClose: false,
      });
    },
    toTop: function () {
      this.$inertia.visit(this.$route("home"));
    },
    $imgAdd(pos, $file){
            var data = new FormData();
      data.append('image', $file);
      axios({
        url: this.$route('media.upload'),
        method: 'post',
        data: data,
        headers: { 'Content-Type': 'multipart/form-data' },
      }).then((url) => {
        console.log(pos, url)
        this.$refs.me.$img2Url(pos, url.data)
      })
    }
  },
};
</script>
