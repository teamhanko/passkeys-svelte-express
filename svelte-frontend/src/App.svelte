<script lang="ts">
  import { Router, Route } from "svelte-routing";
  import LoginPage from "./pages/LoginPage.svelte";
  import HomePage from "./pages/HomePage.svelte";
  import DashboardPage from "./pages/DashboardPage.svelte";
</script>

<Router>
  <Route path="/" component={HomePage} />
  <Route path="/login" component={LoginPage} />
  <Route path="/dashboard" component={DashboardPage} />
</Router>
