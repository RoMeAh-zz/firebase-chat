<script lang="ts">
  import { auth, db, githubProvider } from "./firebase";
  import { authState } from "rxfire/auth";
  import Chat from "./Chat.svelte";

  let user: Record<string, string> = null;

  authState(auth).subscribe(
    (u) =>
      (user = u
        ? { name: u.displayName, email: u.email, photo: u.photoURL }
        : null)
  );
  const login = async () => {
    await auth.signInWithRedirect(githubProvider);
  };

  $: {
    if (![].includes(user.email)) auth.signOut();
  }
</script>

<main>
  {#if user}
    <h3>{user.name}</h3>
    <img src={user.photo} width="100" alt="profile-pic" />
    <button on:click={() => auth.signOut()}>Logout</button>
    <hr />
    <Chat {user} />
  {:else}
    <button on:click={login}> Sign in with Github </button>
  {/if}
</main>

<style>
</style>
