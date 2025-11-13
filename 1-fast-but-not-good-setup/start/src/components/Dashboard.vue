<script setup>
import { onMounted, reactive, onBeforeMount } from "vue";
import { useRouter } from "vue-router";
import { initializeApp } from "firebase/app";
import {
  collection,
  doc,
  getFirestore,
  onSnapshot,
  setDoc,
} from "firebase/firestore";
import { config } from "../config";

const firebaseApp = initializeApp(config.firebase);
const firestore = getFirestore(firebaseApp);
const markdownCol = collection(firestore, "markdowns");
const state = reactive({ markdowns: [] });
const router = useRouter();

onBeforeMount(async () => {
  // Get a user
});

onMounted(() => {
  onSnapshot(markdownCol, (snapshot) => {
    const data = snapshot.docs.map((d) => ({
      ...d.data(),
      id: d.id,
    }));

    state.markdowns = data;
  });
});

async function newMarkdown() {
  const docRef = doc(collection(firestore, "markdowns"));
  await setDoc(docRef, {
    markdown: "",
    converted: "",
  });
  router.push(`/editor/${docRef.id}`);
}
</script>

<template>
  <h1>I am the dashboard</h1>

  <ul v-for="markdown in state.markdowns" :key="markdown.id">
    <li>
      <router-link :to="{ path: `/editor/${markdown.id}` }">{{
        markdown.id
      }}</router-link>
    </li>
  </ul>

  <button @click="newMarkdown">New</button>
</template>
