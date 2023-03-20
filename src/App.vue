<script setup>
import { ref } from "vue";

const STORAGE_KEY = "memo-app";

let memos = ref(JSON.parse(localStorage.getItem(STORAGE_KEY) || "[]"));
let editedMemo = ref({});

function viewMemo(memo) {
  editedMemo.value = memo;
  editedMemo.value.isEdit = true;
}

function addMemo() {
  memos.value.push({
    id: Date.now(),
    content: "新規メモ",
    title: "新規メモ",
    isEdit: false,
  });
  saveMemos(memos);
  viewMemo(memos.value[memos.value.length - 1]);
}

function saveMemos(memos) {
  localStorage.setItem(STORAGE_KEY, JSON.stringify(memos.value));
}

function saveEditedMemo(memo) {
  if (!memo.isEdit) {
    return;
  }
  if (memo.content === "") {
    deleteMemo(memo);
    return;
  }
  memo.content.includes("\n")
    ? (memo.title = memo.content.split("\n", 1)[0])
    : (memo.title = memo.content);
  memo.isEdit = false;
  const index = memos.value.indexOf(memo);
  memos.value.splice(index, 1, memo);
  localStorage.setItem(STORAGE_KEY, JSON.stringify(memos.value));
}

function deleteMemo(memo) {
  const index = memos.value.indexOf(memo);
  if (index !== -1) {
    memos.value.splice(index, 1);
    saveMemos(memos);
  }
  editedMemo.value = {};
}
</script>

<template>
  <h1>Memo app</h1>
  <section id="memo_app">
    <div class="app">
      <div class="memo_list">
        <ul>
          <li v-for="memo in memos" v-bind:key="memo.id">
            <label class="memo clickable" v-on:click="viewMemo(memo)">{{
              memo.title
            }}</label>
          </li>
          <li
            id="add_memo_text"
            style="list-style-type: none"
            v-on:click="addMemo()"
          >
            +
          </li>
        </ul>
      </div>
      <div class="form_area">
        <form v-on:submit.prevent>
          <textarea
            cols="30"
            rows="3"
            class="text_area"
            v-model="editedMemo.content"
          ></textarea>
          <button class="edit_button" v-on:click="saveEditedMemo(editedMemo)">
            編集
          </button>
          <button class="delete_button" v-on:click="deleteMemo(editedMemo)">
            削除
          </button>
        </form>
      </div>
    </div>
  </section>
</template>

<style scoped>
#memo_app {
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

.clickable {
  cursor: pointer
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
</style>
