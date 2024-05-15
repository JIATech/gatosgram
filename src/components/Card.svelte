<script>
    import { onMount } from 'svelte';
    import Comments from './Comments.svelte';
    import Modal from './Modal.svelte';
    import Share from './Share.svelte';

    import { blur } from 'svelte/transition';

    import { likeCount } from '../store/store.js';

    import { writable } from 'svelte/store';

    import { createEventDispatcher } from 'svelte';
    const dispatch = createEventDispatcher();

    export let username;
    export let location;
    export let photo;
    export let postComment;
    export let comments;
    export let avatar;

    let isModal = false;
    let like = false;
    let bookmark = false;
    let showComments = writable(true);
    let scrollTop = 0;

    function handleModal() {
      scrollTop = window.scrollY;
      isModal = !isModal;
    }

    function handleLike() {
        like = !like;
        if (like) {
            likeCount.update(n => n + 1);
        } else {
            likeCount.update(n => n - 1);
        }
    }

    function handleBookmark() {
        bookmark = !bookmark;
    }

    function handleCommentsClick() {
        showComments.update(n => !n);
    }

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
  .Card {
    border: 1px solid rgba(219, 219, 219, 1);
    border-radius: 4px;
    background-color: white;
    margin: 0 0 2em 0;
  }
  .Card-Header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 1em;
  }
  .Card-user {
    display: flex;
    align-items: center;
    justify-content: flex-end;
  }
  .Card-user img {
    width: 32px;
    height: 32px;
    border-radius: 50%;
  }
  .Card-user h2 {
    margin: 0;
    padding: 0;
    font-size: 14px;
    font-weight: 600;
    margin: 0 0 0 1em;
    color: black;
  }
  .Card-user h2 span {
    display: block;
    font-size: 12px;
    font-weight: normal;
    color: rgba(38, 38, 38, 0.7);
  }
  .Card-photo {
    padding: 0;
    margin: 0;
  }
  .Card-photo img {
    width: 100%;
    height: auto;
  }
  .Card-photo figure {
    margin: 0;
    padding: 0;
    cursor: pointer;
  }
  .Card-settings i {
    cursor: pointer;
  }
  .Card-icons {
    padding: 1em;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  .Card-icons i {
    margin: 0 1em 0 0;
    cursor: pointer;
    font-size: 20px;
  }
  .Card-icons i:last-child {
    margin: 0;
  }
  .Card-description {
    padding: 0 1em 1em 1em;
  }
  .Card-description h3 {
    font-size: 14px;
    font-weight: bold;
    color: black;
  }
  .Card-description span {
    font-size: 14px;
  }
  .active-like {
    color: #bc1888;
    animation: bounce linear 0.8s;
    animation-iteration-count: 1;
    transform-origin: 20% 20%;
  }
  .active-bookmark {
    color: #f09433;
    animation: bounce linear 0.8s;
    animation-iteration-count: 1;
    transform-origin: 20% 20%;
  }

  @keyframes bounce {
    0% {
      transform: translate(0px, 0px);
    }
    15% {
      transform: translate(0px, -25px);
    }
    30% {
      transform: translate(0px, 0px);
    }
    45% {
      transform: translate(0px, -15px);
    }
    60% {
      transform: translate(0px, 0px);
    }
    75% {
      transform: translate(0px, -5px);
    }
    100% {
      transform: translate(0px, 0px);
    }
  }
</style>

<div class="Card">

{#if isModal}
  <div transition:blur>
    <Modal on:close={handleModal} styles={`width: 100%; height: 100%; position: absolute; top: ${scrollTop}px; left: 0; background-color: rgba(0, 0, 0, 0.8); z-index: 100;`}>
      <Share on:click={handleModal} />
    </Modal>
  </div>
{/if}

    <div class="Card-container">
        <div class="Card-Header">
            <div class="Card-user">
                <img src={avatar} alt={username} />
                <h2>{username}
                    <span>{location}</span>
                </h2>
            </div>
            <div class="Card-header-info">
                <div class="Card-settings">
                    <i class="fas fa-ellipsis-h" />
                <div class="Card-header-info-date">2 hours ago</div>
                </div>
            </div>
        </div>
        <div class="Card-photo">
            <figure on:dblclick={handleLike}>
                {#if like}
                    <img src={photo} alt={username} />
                {:else}
                    <img src={photo} alt={username} />
                {/if}
              </figure>
        </div>
        <div class="Card-icons">
            <!-- svelte-ignore a11y-no-static-element-interactions -->
            <div class="Card-icons-left">
                <!-- svelte-ignore a11y-click-events-have-key-events -->
                <!-- svelte-ignore a11y-no-static-element-interactions -->
                <i class="far fa-heart" 
                  class:active-like={like}
                  on:click={handleLike}
                  aria-label="Like Post"
                />
                <!-- svelte-ignore a11y-click-events-have-key-events -->
                <i class="far fa-comment" on:click={handleCommentsClick} />
                  <!-- svelte-ignore a11y-click-events-have-key-events -->
                  <!-- svelte-ignore a11y-no-static-element-interactions -->
                  <i class="far fa-paper-plane" on:click={handleModal} />
            </div>
            <!-- svelte-ignore a11y-click-events-have-key-events -->
            <div class="active-bookmark">
                <!-- svelte-ignore a11y-click-events-have-key-events -->
                <!-- svelte-ignore a11y-no-static-element-interactions -->
                <i class="far fa-bookmark"
                class:active-bookmark={bookmark}
                on:click={handleBookmark}
                />
            </div>
            <div class="Card-description">
                <h3>{username}</h3>
                <span>{postComment}</span>
            </div>
            <div class="Card-comments">
              {#if $showComments}
                <Comments {comments} />
              {/if}
            </div>
        </div>
    </div>
</div>
