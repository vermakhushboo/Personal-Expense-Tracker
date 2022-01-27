<script>
    import { Route } from 'svelte-routing';
    import { token } from './stores.js';
    import dashboard from "./dashboard.svelte";
    import accessDenied from "./accessDenied.svelte";

    $: isAuthenticated = $token;
</script>

{#if isAuthenticated}
    <Route path="/dashboard" component={dashboard} />
{:else}
    <Route path="/dashboard" component={accessDenied} />
{/if}