<template>
  <div class="row historyList" :style="{'height':height}">
    <div class="col-sm-12">
      <div class="alert alert-default" v-if="!histories.length">
        When you press the Save button, it will be added to the history.
      </div>
      <div class="thumbnail" v-for="(history, key, index) in histories" :key="index">
        <img v-lazy="createUrl(history.encodedText)" @click="read(history.text, $event)" height="200" width="100%">
        <div class="caption">
          <div class="row">
            <div class="col-sm-4">
              <button type="button" class="close pull-left" @click="del(history.id, $event)">&times;</button>
            </div>
            <div class="col-sm-8 text-right">
              {{history.created}}
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
/* @flow */

export default {
  name: 'historyList',
  props: {
    height: {
      type: String,
      default: '100%'
    }
  },
  data(): any {
    return {
      deleteMessage: 'Can I delete it?',
      editMessage: 'Do you want to edit it?'
    }
  },
  computed: {
    histories(): any {
      return this.$store.state.histories.data
    }
  },
  mounted() {
    this.$store.dispatch('histories/getHistories')
    this.setLazyloadEvent()
  },
  methods: {
    createUrl(encodedText: string): string {
      const extension: string = 'png'
      return `${
        this.$store.state.plantumlEditor.cdn
      }${extension}/${encodedText}.${extension}`
    },
    setLazyloadEvent() {
      this.$Lazyload.$on('loaded', ({ el, naturalHeight }: any) => {
        el.height = naturalHeight
      })
    },
    del(id: number) {
      if (window.confirm(this.deleteMessage)) {
        this.$store.dispatch('histories/delete', id)
      }
    },
    read(text: string) {
      if (window.confirm(this.editMessage)) {
        this.$store.dispatch('plantumlEditor/renderUML', text)
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.historyList {
  margin-top: -20px;
  padding-top: 20px;
  overflow-y: auto;
  background-color: #002b36;
}
.historyList .thumbnail {
  background-color: #fffff;
  border-color: #002b36;
}
.historyList img {
  cursor: pointer;
  &[lazy='loading'] {
    background-color: #f5f5f5;
  }
  &[lazy='loaded'] {
    background-color: none;
  }
}
</style>
