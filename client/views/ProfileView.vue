<script setup lang="ts">
import EventPreviewComponent from "@/components/Event/EventPreviewComponent.vue";
import { useUserStore } from "@/stores/user";
import { fetchy } from "@/utils/fetchy";
import { storeToRefs } from "pinia";
import { onBeforeMount, ref } from "vue";
const { currentUsername, isLoggedIn } = storeToRefs(useUserStore());
const username = currentUsername.value;

const loaded = ref(false);

let upcomingEvents = ref<Array<Record<string, string>>>([]);
let pinnedTrails = ref<Array<Record<string, string>>>([]);
let allTrails = ref<Array<Record<string, string>>>([]);

const accessToken = "pk.eyJ1IjoiZmFuZ2siLCJhIjoiY2t3MG56cWpjNDd3cjJvbW9iam9sOGo1aSJ9.RBRaejr5HQqDRQaCIBDzZA";

async function getUpcomingEvents() {
  let registeredEvents;
  try {
    registeredEvents = await fetchy(`api/events/registered`, "GET", { query: { user: username } });
  } catch (_) {
    return;
  }

  // TODO: filter registeredEvents for those that are occuring in the future (ie. do not display past events)
  upcomingEvents.value = registeredEvents;
}

async function getAllTrails() {
  let usersTrails;
  try {
    usersTrails = await fetchy(`api/trails/`, "GET", { query: { author: username } });
  } catch (_) {
    return;
  }
  allTrails.value = usersTrails;
  pinnedTrails.value = usersTrails.filter((trail) => trail.pinned);
}

onBeforeMount(async () => {
  await getUpcomingEvents();
  await getAllTrails();
  loaded.value = true;
});
</script>

<template>
  <main id="profile-container">
    <section id="profile-section">
      <div id="avatar-container">
        <img id="avatar-img" src="https://via.placeholder.com/100x100/cf5" />
        <!-- <input type="file" /> -->
      </div>
      <h1>{{ currentUsername }}</h1>
    </section>

    <section>
      <h3>Upcoming Trails</h3>
      <div>
        <div id="upcoming-trails-list" class="row">
          <article v-for="event in upcomingEvents" :key="event._id">
            <EventPreviewComponent :event="event" />
          </article>
        </div>
      </div>
    </section>

    <section>
      <h3>Pinned Trails</h3>
      <div class="row">
        <article v-for="pinned in pinnedTrails" :key="pinned._id">
          <v-sheet class="pinned-thumbnail"> {{ pinned.name }} </v-sheet>
        </article>

        <article v-for="x in Array(5 - pinnedTrails.length).keys()" :key="x">
          <v-sheet class="pinned-thumbnail add-pinned-button">
            <v-icon color="white">mdi-plus</v-icon>
          </v-sheet>
        </article>
      </div>
    </section>

    <section>
      <h3>All Trails</h3>
      <!-- <MapContainer /> -->
      <!-- <mapbox-map :accessToken="accessToken" mapStyle="mapbox://styles/fangk/cln4vt2gg06w901qrgym22cdp" /> -->
    </section>
  </main>
</template>

<style scoped>
main {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  row-gap: 4em;
  padding: 2em;
}

h1,
h2,
h3 {
  color: white;
}

.pinned-thumbnail {
  border-radius: 25px;
  width: calc((100vw - 8em) / 5);
  aspect-ratio: 1;
}

.add-pinned-button {
  background-color: rgba(0, 0, 0, 0);
  border: 1px dashed white;
  display: flex;
  justify-content: center;
  align-items: center;
}

.row {
  display: flex;
  flex-direction: row;
  column-gap: 1em;
}

#upcoming-trails-list {
  width: 100%;
  overflow-x: scroll;
}

#profile-container {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

#profile-section {
  display: flex;
  flex-direction: row;
  justify-content: flex-start;
  column-gap: 30px;
}

#avatar-container {
  width: 150px;
  height: 150px;
  border-radius: 50%;
  background-color: rgb(255, 255, 255);
}

#avatar-img {
  transition: opacity 0.3s;
  opacity: 1;
  object-fit: cover;
  border-radius: 50%;
  height: 100%;
  width: 100%;
}

#avatar-img:hover {
  opacity: 0.4;
}
</style>