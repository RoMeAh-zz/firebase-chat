<script lang="ts">
  import { db } from "./firebase";
  import { collectionData } from "rxfire/firestore";
  import { startWith } from "rxjs";

  export let user;
  let message = "";

  let messages = collectionData(
    db.collection("messages").orderBy("createdAt").limit(25),
    "id"
  ).pipe<any[]>(startWith([]));

  const send = () => {
    if (message.trim().length == 0) return;
    db.collection("messages").add({
      ...user,
      content: message,
      createdAt: new Date(),
    });
    message = "";
  };
</script>

<main>
  <div class="messages">
    {#each $messages as message}
      <div class={`message ${message.email == user.email ? "me" : "other"}`}>
        {message.content}
      </div>
    {/each}
  </div>
  <div class="form">
    <input type="text" bind:value={message} />
    <button type="submit" on:click={send}>Send!</button>
  </div>
</main>

<style>
</style>
