<script lang="ts">
  import { navigate } from "svelte-routing";
  import { create } from "@github/webauthn-json";

  async function handleLogout() {
    try {
      const response = await fetch("http://localhost:5001/api/logout", {
        method: "POST",
        credentials: "include",
      });
      if (response.ok) {
        console.log("Logout successful:", await response.json());
        navigate("/login"); // Assuming you have a route named '/login'
      } else {
        console.error("Logout failed:", await response.json());
      }
    } catch (error) {
      console.error("Logout error:", error);
    }
  }

  async function registerPasskey() {
    try {
      const createOptionsResponse = await fetch(
        "http://localhost:5001/api/passkeys/register",
        {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          credentials: "include",
          body: JSON.stringify({
            start: true,
            finish: false,
            credential: null,
          }),
        }
      );

      const { createOptions } = await createOptionsResponse.json();
      console.log("createOptions", createOptions);

      const credential = await create(createOptions);
      console.log(credential);

      const response = await fetch(
        "http://localhost:5001/api/passkeys/register",
        {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          credentials: "include",
          body: JSON.stringify({ start: false, finish: true, credential }),
        }
      );

      if (response.ok) {
        console.log("Passkey registered successfully");
      } else {
        console.error("Error registering passkey");
      }
    } catch (error) {
      console.error("Error during passkey registration:", error);
    }
  }
</script>

<div class="p-4">
  <button on:click={handleLogout}>Logout</button>
  <div style="margin-top: 16px;">
    <button on:click={registerPasskey}> Register a new passkey </button>
  </div>
</div>
