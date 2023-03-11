<script setup>
import { ref } from 'vue'

const STORAGE_KEY = 'memo-app'

let memos = ref(JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]'))
let editedMemo2 = ref({})

function viewMemo(memo) {
  editedMemo2.value = memo
}

function addMemo2() {
  memos.value.push({
    id: Date.now(),
    content: '新規メモ',
    title: '新規メモ',
  })
  saveMemos2(memos)
  viewMemo(memos.value[memos.value.length - 1])
}

function saveMemos2(memos) {
  localStorage.setItem(STORAGE_KEY, JSON.stringify(memos.value))
}

function saveEditedMemo(memo) {
  const memoIds = memos.value.map((memo) => memo.id)
  const index = memoIds.indexOf(memo.id)
  memos.value[index].content = memo.content
  memos.value[index].title = memo.content.split('\n', 1)[0]
  localStorage.setItem(STORAGE_KEY, JSON.stringify(memos.value))
  editedMemo2.value = ''
}

function deleteMemo(memo) {
  const index = memos.value.indexOf(memo)
  if (index !== -1) {
    memos.value.splice(index, 1)
    saveMemos2(memos)
  }
  editedMemo2.value = {}
}

function cancelEdit() {
  editedMemo2.value = {}
}
</script>

<template>
  <h1>Memo app</h1>
  <section id="memo_app">
    <div class="app">
      <div class="memo_list">
        <ul>
          <li v-for="memo in memos" v-bind:key="memo.id">
            <label class="memo" v-on:click="viewMemo(memo)">{{ memo.title }}</label>
          </li>
          <li id="add_memo_text" style="list-style-type: none" v-on:click="addMemo2()">+</li>
        </ul>
      </div>
      <div class="form_area">
        <form v-on:submit.prevent>
          <textarea cols="30" rows="3" class="text_area" v-model="editedMemo2.content"
            v-on:blur="cancelEdit(editedMemo2)"></textarea>
          <button class="edit_button" v-on:click="saveEditedMemo(editedMemo2)">編集</button>
          <button class="delete_button" v-on:click="deleteMemo(editedMemo2)">削除</button>
        </form>
      </div>
    </div>
  </section>
</template>

<style scoped>
#memo_app {
  background-color: lightgoldenrodyellow;
  padding: 5px;
  width: 450px;
  border: 1px solid darkgray;
  border-radius: 5px;
  height: auto;
}

.app {
  display: flex;
  padding-top: 5px;
  justify-content: space-between;
}

.memo_list {
  width: 150px;
}

#add_memo_text {
  font-size: 1.3rem;
  color: blue;
  text-decoration: underline;
}

.form_area {
  width: 250px;
}

.text_area {
  height: 200px;
  width: 100%;
}

.edit_button {
  width: 150px;
  margin: 0 5px 0 0;
}

.delete_button {
  width: 95px;
  margin: 0 0 0 0;
}

.memo {
  white-space: pre-line;
}

header {
  line-height: 1.5;
  max-height: 100vh;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

nav {
  width: 100%;
  font-size: 12px;
  text-align: center;
  margin-top: 2rem;
}

nav a.router-link-exact-active {
  color: var(--color-text);
}

nav a.router-link-exact-active:hover {
  background-color: transparent;
}

nav a {
  display: inline-block;
  padding: 0 1rem;
  border-left: 1px solid var(--color-border);
}

nav a:first-of-type {
  border: 0;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }

  nav {
    text-align: left;
    margin-left: -1rem;
    font-size: 1rem;

    padding: 1rem 0;
    margin-top: 1rem;
  }
}
</style>
