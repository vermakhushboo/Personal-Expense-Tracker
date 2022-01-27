<script>
    import { Router, Route } from 'svelte-routing';
    import ProtectedRoute from './ProtectedRoute.svelte';

    import Home from './Home.svelte';
    import Dashboard from './Dashboard.svelte';
</script>

<Router>
    <Route path="/" component={Home} />
    <ProtectedRoute path="/dashboard" component={Dashboard} />
</Router>