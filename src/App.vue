<template>
  <div class="px-4 mx-auto max-w-7xl sm:px-6 lg:px-8 bg-gray-800">
    <div class="text-4xl font-bold p-20 text-center text-gray-200">
      EOS Super Friends!
    </div>
    <div
      class="max-w-7xl mx-auto my-6 flex flex-col justify-center md:flex-row flex-wrap basis-0 shrink grow gap-4"
    >
      <div
        class="rounded-lg bg-gray-500 w-4/6 p-10 max-w-sm flex flex-col justify-center m-auto "
        v-for="member in members"
        :key="member.name"
      >
        <div class="max-w-lg flex flex-col justify-center basis-0 shrink grow ">
          <img
            class="rounded-lg w-40 h-40 mx-auto"
            :src="member.picture"
            :alt="member.name"
          />
          <h1 class="font-semibold text-gray-200 text-center mt-4 text-2xl">
            {{ member.name }}
          </h1>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
  components: {},
  setup() {
    const members: { name: string; picture: string }[] = [
      {
        name: "Aaron Cox",
        picture: "Qmb7yzsVfEssigjHbcX46g4RPPgvEzjqiAxa1nmkm76APo"
      },
      {
        name: "Brandon Lovejoy",
        picture: "QmVpVRdr5U7XuWprRUhr4tDEjitxV1b7mf4Ui78ba81fkV"
      },
      {
        name: "Chris Barnes",
        picture: "QmWyawMvUQMf7FmaxpJyot5efmVFWC9gF8JVUcR9ovicwX"
      },
      {
        name: "Randall Roland",
        picture: "QmaXBFy6Xh3K7NM5BHJhzYXgTmbVzCgdpn7YeBiqhAsCQv"
      },
      {
        name: "Jesse Jaffe",
        picture: "QmSx7cT8WoPR9tyDJh5D4snjZcMYqiiXCCnKWYMJJpLs2Q"
      },
      {
        name: "John Williamson",
        picture: "QmWU7jyrFxQF5LfMrKyCdb4b1jt4fzDsk3w2KHJ2mvjn5F"
      }
    ].map(x => ({
      ...x,
      picture: `https://ipfs.orelo.software/ipfs/${x.picture}`
    }));

    return { members };
  }
});
</script>

<style>
body {
  background: "black";
}
</style>
