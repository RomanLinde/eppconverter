<script>
  import Counter from './lib/Counter.svelte'
</script>

<main>
  <h1>Import danych z HTML do pliku EPP</h1>
  
  <p>
    Wskaż plik zawierający dane w postaci HTML a następnie kliknij "Wykonaj".
  </p>

  <div class="card">
    <Counter />
  </div>


  <p class="read-the-docs">
    Click on the Vite and Svelte logos to learn more
  </p>
</main>

<style>
  .read-the-docs {
    color: #888;
  }
</style>
