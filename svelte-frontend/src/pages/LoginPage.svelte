<script lang="ts">
    import { navigate } from "svelte-routing";
    import { get } from '@github/webauthn-json';
  
    let email = '';
    let password = '';
  
    async function loginUser() {
      try {
        const response = await fetch('http://localhost:5001/api/login', {
          method: 'POST',
          credentials: 'include',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({ email, password })
        });
        if (!response.ok) {
          throw new Error('Login failed');
        }
        const data = await response.json();
        console.log('Login successful', data);
        navigate('/dashboard'); // Assuming you have a route named '/dashboard'
      } catch (error) {
        console.error('Login error:', error);
      }
    }
  
    async function signInWithPasskey() {
      try {
        const createOptionsResponse = await fetch("http://localhost:5001/api/passkeys/login", {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          credentials: 'include',
          body: JSON.stringify({ start: true, finish: false, credential: null }),
        });
  
        const { loginOptions } = await createOptionsResponse.json();
        const options = await get(loginOptions);
  
        const response = await fetch("http://localhost:5001/api/passkeys/login", {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          credentials: 'include',
          body: JSON.stringify({ start: false, finish: true, options }),
        });
  
        if (response.ok) {
          console.log("User logged in with passkey");
          navigate('/dashboard'); // Assuming you have a route named '/dashboard'
        } else {
          throw new Error("Login with passkey failed");
        }
      } catch (error) {
        console.error("Login with passkey error:", error);
      }
    }
  </script>
  
  <form on:submit|preventDefault={loginUser}>
    <div>
      <input id="email" required name="email" type="email" bind:value={email} autoComplete="email" placeholder="Email"/>
      <input
        id="password"
        name="password"
        type="password"
        bind:value={password}
        autoComplete="current-password"
        placeholder="Password"
      />
    </div>
    <button type="submit" style="margin-top: 16px;">Sign in with Email</button>
  </form>
  <button on:click={signInWithPasskey} style="margin-top: 16px;">Sign in with Passkey</button>

  <style>
    form {
      display: flex;
      flex-direction: column;
      align-items: center;
      margin-top: 16px;
    }
  
    input {
      margin-bottom: 8px;
      padding: 8px;
      border: 1px solid #ccc;
      border-radius: 4px;
    }
  </style>