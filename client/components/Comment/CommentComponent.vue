<script setup lang="ts">
import { useUserStore } from "@/stores/user";
import { storeToRefs } from "pinia";
import { fetchy } from "../../utils/fetchy";
import { formatDate } from "../../utils/formatDate";

const props = defineProps(["comment"]);
const emit = defineEmits(["refreshComments"]);
const { currentUsername } = storeToRefs(useUserStore());

const deleteComment = async () => {
  try {
    await fetchy(`/api/comments/${props.comment._id}`, "DELETE");
  } catch {
    return;
  }
  emit("refreshComments");
};
</script>

<template>
  <p class="author">
    <RouterLink :to="{ name: 'Profile', params: { user: props.comment.author } }"> {{ props.comment.author }}</RouterLink>
  </p>
  <p>{{ props.comment.content }}</p>
  <div class="base">
    <menu v-if="props.comment.author == currentUsername">
      <li>
        <button class="button-error btn-small" @click="deleteComment"><v-icon>mdi-close</v-icon></button>
      </li>
    </menu>
  </div>
  <article class="timestamp">
    <p>Created on: {{ formatDate(props.comment.dateCreated) }}</p>
  </article>
</template>

<style scoped>
p {
  margin: 0em;
}

.author {
  font-weight: bold;
  font-size: 1em;
}

menu {
  list-style-type: none;
  display: flex;
  flex-direction: row;
  gap: 1em;
  padding: 0;
  margin: 0;
}

.timestamp {
  display: flex;
  justify-content: flex-end;
  font-size: 0.9em;
  font-style: italic;
}

a {
  color: black;
}

.base {
  display: flex;
  justify-content: end;
  align-items: center;
}

.base article:only-child {
  margin-left: auto;
}
</style>
