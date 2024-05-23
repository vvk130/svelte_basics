<script>
  import { onMount } from "svelte";

  let messages = [];
  let eventSource;

  onMount(() => {
    eventSource = new EventSource("/api/json-sse");
    // Answer ongoing course hidden
    };
  });
</script>

<main>
  <h1>Messages</h1>
  <ul>
    <!-- Answer ongoing course hidden -->
  </ul>
</main>
