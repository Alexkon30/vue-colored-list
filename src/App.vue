<template>
<div class="app">
  <div class="message" v-for="message in currentMessages" :key="message.date">
    <div class="message__header">
      {{convertDate(message.date)}} / <strong>{{message.authorName}}</strong> / {{message.authorUrl}}
    </div>
    <div class="message__body" v-html="addColors(message.content, message.contentPostTones)">
    </div>
  </div>
  <div class="footer">
    <div class="spinner-border" role="status" v-if="isLoad">
      <span class="visually-hidden">Loading...</span>
    </div>
    <button @click="addMessages" v-else-if="abilityToLoadMore">Add more</button>

  </div>
</div>
</template>

<script>
export default {
  data() {
    return {
      messages: require('./data.json'),
      currentMessages: require('./data.json').filter((item, i) => i < 10),
      months: ['января', 'февраля', 'марта', 'апреля', 'мая', 'июня', 'июля', 'августа', 'сентября', 'октября', 'ноября', 'декабря'],
      index: 10,
      isLoad: false,
      abilityToLoadMore: true
    }
  },
  methods: {
    async addMessages() {
      return new Promise(res => {
          this.isLoad = true;
          setTimeout(res, 1000)
        })
        .then(() => {
          let maxIndex = this.messages.length - 1
          for (let i = this.index; i < this.index + 10; i++) {

            if (i === maxIndex) {
              this.index = maxIndex
              this.isLoad = false
              this.abilityToLoadMore = false
              return
            }

            this.currentMessages.push(this.messages[i])
          }
          this.isLoad = false;
          this.index += 10;
        })
    },
    addColors(str = '', arrOfColors = []) {
      if (arrOfColors.length === 0 || str.length === 0) return str

      let result = ''
      let index = 0

      for (let i = 0; i < arrOfColors.length; i++) {
        result += str.slice(index, arrOfColors[i].position)

        result += `<span style="background-color: hsl(${120 * arrOfColors[i].tone}, 80%, 80%)">`
        result += str.slice(arrOfColors[i].position, arrOfColors[i].position + arrOfColors[i].length)
        result += '</span>'

        index = arrOfColors[i].position + arrOfColors[i].length

      }

      result += str.slice(index)

      return result
    },
    convertDate(str) {
      let dateOfMessage = new Date(Date.parse(str))

      let minutes = `${dateOfMessage.getMinutes()}`.length > 1 ? dateOfMessage.getMinutes() : `0${dateOfMessage.getMinutes()}`
      let hours = `${dateOfMessage.getHours()}`.length > 1 ? dateOfMessage.getHours() : `0${dateOfMessage.getHours()}`
      let date = dateOfMessage.getDate()
      let month = dateOfMessage.getMonth() + 1
      let year = dateOfMessage.getFullYear()

      return `${hours}:${minutes}, ${date} ${this.months[month]} ${year} г.`
    }
  }
}
</script>

<style>
.app {
  margin: 20px;
}

.message {
  padding: 10px;
}

.message:not(:last-child) {
  border-bottom: 1px solid gray;
}

.message__header {
  margin-bottom: 15px;
}

.footer {
  text-align: center;
  margin-top: 15px;
}

button {
  padding: 6px;
  border-radius: 6px;
  background-color: lightgreen;
  color: darkgreen;
}
</style>
