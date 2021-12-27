<script>
  import { onMount } from 'svelte';
  import { Link } from 'svelte-routing';  

  const providers = ['twitter', 'github', 'aad'];
  const redirect = window.location.pathname;
  let userInfo = undefined;

  onMount(async () => (userInfo = await getUserInfo()));

  async function getUserInfo() {
    try {
      const response = await fetch('/.auth/me');
      const payload = await response.json();
      const { clientPrincipal } = payload;
      return clientPrincipal;
    } catch (error) {
      console.error('No profile could be found');
      return undefined;
    }
  }

  function getProps({ href, isPartiallyCurrent, isCurrent }) {
    const isActive = href === '/' ? isCurrent : isPartiallyCurrent || isCurrent;

    // The object returned here is spread on the anchor element's attributes
    if (isActive) {
      return { class: 'router-link-active' };
    }
    return {};
  }

</script>

<div class="column is-2">
  <nav class="menu">
    <p class="menu-label">Menu</p>
    <ul class="menu-list">
      <Link to="/products" {getProps}>Products</Link>
      <Link to="/about" {getProps}>About</Link>
    </ul>
  </nav>
  <nav class="menu auth">
    <p class="menu-label">Auth</p>
    <div class="menu-list auth">
      {#if !userInfo}
        {#each providers as provider (provider)}
          <a href={`/.auth/login/${provider}?post_login_redirect_uri=${redirect}`}>
          <!--<a href={`https://awesomettt.b2clogin.com/AwesomeTTT.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_sign-up-and-sign-in&client_id=5b7e0f4e-3282-461f-863a-3cb64959d189&nonce=defaultNonce&redirect_uri=http%3A%2F%2Flocalhost%3A5000&scope=openid&response_type=code&prompt=login&code_challenge_method=S256&code_challenge=xgSdvSaZhshBz4dg4-GavPiktz46xRmKM-eXV8ka5P0`}-->
            {provider}
          </a>
        {/each}
      {/if}
      {#if userInfo}
        <a href={`/.auth/logout?post_logout_redirect_uri=${redirect}`}>
          Logout
        </a>
      {/if}
    </div>
  </nav>
  {#if userInfo}
  <div class="user">
    <p>Welcome</p>
    <p>{userInfo && userInfo.userDetails}</p>
    <p>{userInfo && userInfo.identityProvider}</p>
  </div>
  {/if}    
</div>