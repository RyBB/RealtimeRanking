<template>
  <div class="hello">
    <table class="ranking-table">
      <thead>
        <tr>
          <th>RANK</th>
          <th>NAME</th>
          <th>SCORE</th>
        </tr>
      </thead>
      <transition-group tag="tbody">
        <tr v-for="(post, index) in allScoreData" :key="post.id">
          <td :class="{newRecord: post.isNew}">{{index + 1}} 位</td>
          <td :class="{newRecord: post.isNew}">{{post.name}}</td>
          <td :class="{newRecord: post.isNew}">{{post.score}}</td>
        </tr>
      </transition-group>
    </table>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data() {
    return {
      allScoreData: [], // {id: Number, name: String, score: Number, isNew: Boolean}
    }
  },
  mounted() {
    const socket = new WebSocket('ws://localhost:5001');

    socket.onmessage = event => {
      console.dir(event.data)
      this.addData(JSON.parse(event.data));
    }
  },
  methods: {
    addData(data) {
      // 既存のスコアのisNewをfalseにする (= 新規レコードのフラグを外す)
      const list = this.allScoreData.map(val => {
        return {
          id: val.id,
          name: val.name,
          score: val.score,
          isNew: false
        };
      });

      list.push({
        id: list.length + 1,
        name: data.name,
        score: Number(data.score),
        isNew: true
      });

      // 並び順をスコアの降順に変更
      this.allScoreData = list.sort((a,b) => (a.score > b.score ? -1 : 1))
    },
  }
}
</script>

<style>
.ranking-table {
  margin: auto;
  width: 50vw;
}
.newRecord {
  background-color: #ff7f50;
}

/* animation */
.v-leave-active,
.v-enter-active {
  transition: opacity .5s, transform .5s;
}
.v-leave-to,
.v-enter {
  opacity: 0;
  transform: translateX(200px);
}
.v-leave,
.v-enter-to {
  opacity: 1;
}
.v-move {
  transition: transform .5s;
}
</style>
