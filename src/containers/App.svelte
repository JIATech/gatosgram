<script>
    import Header from '../components/Header.svelte';
    import Main from '../components/Main.svelte';
    import Sidebar from '../components/Sidebar.svelte';
    import Timeline from '../components/Timeline.svelte';
  let promise = fetch('./gatos.json').then(response => response.json());
</script>

<style>
  @import url("https://fonts.googleapis.com/css?family=Pacifico&display=swap");
  @import url("https://fonts.googleapis.com/css?family=Lato:300,400,600&display=swap");

  :global(body) {
    background-color: #fafafa;
    color: rgba(38, 38, 38, 0.7);
    font-family: "Lato", sans-serif;
    margin: 0;
    padding: 0;
  }
  :global(h1, h2, h3) {
    margin: 0;
    padding: 0;
  }
</style>

<Header />
{#await promise then data}
  <Main>
    <Timeline posts={data.posts} />    
    <Sidebar {...data.user} />
  </Main>
{:catch error}
  <p>Something went wrong: {error.message}</p>
{/await}