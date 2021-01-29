<template class="all">
  <div class="body">
    <h1>Most Starred Github Repos Created In The Last 30 Days.</h1>
    <br />
    <div
      :id="item.id"
      v-for="item in ReposList"
      :key="item.id"
      class="container"
    >
      <div class="avatar">
        <img :src="item.owner.avatar_url" alt="avatar" />
      </div>
      <div class="name">
        <h2>{{ item.name }}</h2>
      </div>
      <div class="description">{{ item.description }}</div>
      <div class="stars">Stars: {{ item.stargazers_count }}</div>
      <div class="issues">Issues: {{ item.open_issues_count }}</div>
      <div class="owner">
        {{ item.created_at.substr(0, 10) }} By {{ item.owner.login }}
      </div>
    </div>
    <div id="pagination">
      <button
        @click="recall_api(false)"
        v-bind:style="{ display: visibility }"
        class="button"
        id="prev_button"
      >
        <span>Previous</span>
      </button>
      <span id="page_number">Page {{ page_nubmer }}</span>
      <button @click="recall_api(true)" class="button" id="next_button">
        <span>Next</span>
      </button>
    </div>
  </div>
</template>
<script>
export default {
  name: "GithubRepos",
  data() {
    return {
      ReposList: [],
      page_nubmer: 0,
    };
  },
  computed: {
    visibility: function () {
      if (this.page_nubmer > 1) return "inline";
      return "none";
    },
  },
  methods: {
    recall_api: function (isNext) {
      isNext ? this.page_nubmer++ : this.page_nubmer--;
      let date = new Date();
      date.setDate(date.getDate() - 30);
      date = date.toISOString();
      fetch(
        `https://api.github.com/search/repositories?q=created:>${date}&sort=stars&order=desc&page=${this.page_nubmer}`,
        {
          method: "get",
        }
      )
        .then((response) => response.json())
        .then((Repos) => {
          console.log(Repos.items);
          this.ReposList = Repos.items;
          scroll(0, 0);
        });
    },
  },
  mounted() {
    this.recall_api(true);
  },
};
</script>

<style scoped>
.body {
  margin-left: 20%;
  margin-right: 20%;
  font-family: Arial, sans-serif;
}
.avatar {
  grid-area: avatar;
  width: 140px;
  height: 128px;
}
.name {
  grid-area: name;
  margin-top: -15px;
  color: #0072b4;
}
.description {
  grid-area: description;
  margin-top: -40px;
  font-size: 13px;
}
.stars {
  grid-area: stars;
  margin-top: -20px;
  font-weight: bold;
}
.issues {
  grid-area: issues;
  margin-top: -20px;
  font-weight: bold;
}
.owner {
  grid-area: owner;
  margin-top: -20px;
  margin-left: -60px;
  font-weight: bold;
}

.container {
  display: grid;
  grid-template-columns: 140px auto auto auto;
  grid-gap: 5px;
  grid-template-areas:
    "avatar name name name"
    "avatar name name name"
    "avatar description description description"
    "avatar stars issues owner";
  margin-bottom: 30px;
  background-color: #ececec;
  border-bottom: 0.5px solid rgb(167, 167, 167);
}
img {
  width: 128px;
  border: 2px;
}
.prev_button {
  display: none;
}
.button {
  display: inline-block;
  border-radius: 4px;
  background-color: #00a2ff;
  border: none;
  color: #ffffff;
  text-align: center;
  font-size: 20px;
  padding: 20px;
  width: 130px;
  transition: all 0.5s;
  cursor: pointer;
  margin: 5px;
}
.button span {
  cursor: pointer;
  display: inline-block;
  position: relative;
  transition: 0.5s;
}
#next_button {
  float: right;
}
#next_button span:after {
  content: "\00bb";
  position: absolute;
  opacity: 0;
  top: 0;
  right: -20px;
  transition: 0.5s;
}
#next_button:hover span {
  padding-right: 25px;
}
#next_button:hover span:after {
  opacity: 1;
  right: 0;
}
#prev_button {
  float: left;
}
#prev_button span:after {
  content: "\00ab";
  position: absolute;
  opacity: 0;
  top: 0;
  left: -20px;
  transition: 0.5s;
}
#prev_button:hover span {
  padding-left: 25px;
}
#prev_button:hover span:after {
  opacity: 1;
  left: 0;
}
#page_number {
  display: inline-block;
  border-radius: 4px;
  background-color: #e4e4e4;
  border: none;
  color: black;
  text-align: center;
  font-size: 15px;
  padding: 20px;
  width: 100px;
  transition: all 0.5s;
  margin: 5px;
}
#pagination {
  box-sizing: border-box;
  text-align: center;
  margin: auto;
  margin-top: 100px;
}
</style>
