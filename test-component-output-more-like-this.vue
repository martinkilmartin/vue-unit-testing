<template>
  <main>
    <div v-if="loading">Loading...</div>
    <template v-else>
      <div v-if="error">Oops! 🤷</div>
      <div v-else>
        <!-- DATA -->
      </div>
    </template>
  </main>
</template>
<script>
describe("when loading", () => {
  it.todo("renders `Loading...` text");
  it.todo("does not render error message");
  it.todo("does not render data");
});

describe("when there is an error", () => {
  it.todo("does not render `Loading...` text");
  it.todo("renders error message");
  it.todo("does not render data");
});
</script>