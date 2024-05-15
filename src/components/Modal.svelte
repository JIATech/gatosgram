<script>
  import { onMount, createEventDispatcher } from 'svelte';
  const dispatch = createEventDispatcher();
  function handleClickOutside() {
    dispatch('close');
  }
  // Escucha el evento 'close' y cierra el modal
  function handleClose() {
    dispatch('close');
  }
  // Agrega el controlador de eventos al montar el componente
  onMount(() => {
    function handleEscape(event) {
      if (event.key === 'Escape') {
        dispatch('close');
      }
    }
    window.addEventListener('keydown', handleEscape);
    return () => {
      window.removeEventListener('keydown', handleEscape);
    };
  });
</script>

<style>
  .Modal {
    position: relative;
  }
  .Modal-overlay {
    background-color: rgba(0, 0, 0, 0.7);
    position: fixed;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    z-index: 8;
    cursor: pointer;
  }
</style>

<!-- svelte-ignore a11y-no-static-element-interactions -->
<div class="Modal">
    <!-- svelte-ignore a11y-click-events-have-key-events -->
    <div class="Modal-overlay" on:click={handleClickOutside} />
    <slot />
</div>