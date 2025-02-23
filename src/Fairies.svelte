<script>
  import { customAlphabet } from "nanoid";
  import FairyClippy from "./components/FairyClippy.svelte";
  import FairyMap from "./components/FairyMap.svelte";
  import FairySidebar from "./components/FairySidebar.svelte";
  import { makeFairyStore } from "./stores";

  export let meta;
  document.title = "Eureka Fairy Tracker";

  const idgen = customAlphabet("qwertyuiopasdfghjklzxcvbnm", 6);
  let pwdInUrl = false;

  let id = meta?.params?.id;
  if (!id) {
    id = idgen();
    history.pushState(null, "", `/fairies/${id}`);
  }

  const store = makeFairyStore(id, meta?.query?.pwd);

  let pwd_field = "";

  function sendPassword() {
    store.auth(pwd_field);
  }

  const switchZone = (zone) => () => {
    store.setZone(zone, $store.password);
  };
</script>

<div class="container-fluid">
  {#if $store.password}
    <div class="row my-10">
      <div class="col-12">
        <div class="btn-group w-full">
          <button
            class="btn btn-sm"
            class:btn-success={$store.zone === "anemos"}
            on:click={switchZone("anemos")}
          >Anemos
          </button>
          <button
            class="btn btn-sm"
            class:btn-secondary={$store.zone === "pagos"}
            on:click={switchZone("pagos")}
          >Pagos
          </button>
          <button
            class="btn btn-sm"
            class:btn-danger={$store.zone === "pyros"}
            on:click={switchZone("pyros")}
          >
            Pyros
          </button>
          <button
            class="btn btn-sm"
            class:btn-primary={$store.zone === "hydatos"}
            on:click={switchZone("hydatos")}
          >Hydatos
          </button>
        </div>
      </div>
    </div>
  {/if}
  <div class="row">
    <div class="col-md-8">
      <FairyMap
        zone="anemos"
        store={$store}
        set={store.set}
        suggest={store.suggest}
      />
    </div>
    <div class="col-md-4">
      <FairySidebar store={$store}
                    del={store.del}
                    set={store.set}
                    suggest={store.suggest}
                    acceptSuggestion={store.acceptSuggestion}
                    delSuggestion={store.delSuggestion} />
      <div class="card mt-20">
        {#if $store.conn}
          <div class="badge badge-pill badge-success">Connected!</div>

          <p class="mt-10">
            Your state is synchronized with anyone else who has this URL.
          </p>

          <div class="mt-10">
            <strong>Share:</strong>
            <input
              type="text"
              class="form-control"
              readonly
              value={pwdInUrl ? `https://ovni.cool/f/${id}?pwd=${$store.password}` : `https://ovni.cool/f/${id}`}
            />

            {#if $store.password}
              <div class="custom-checkbox mt-5">
                <input type="checkbox"
                       bind:checked={pwdInUrl}
                       id="showPassword" />
                <label for="showPassword">Copy password in URL?</label>
              </div>
            {/if}
          </div>

          {#if !$store.password}
            <p class="mt-10">Enter the tracker password to make changes:</p>
            <div class="input-group">
              <input type="text"
                     class="form-control"
                     bind:value={pwd_field} />
              <div class="input-group-append">
                <button class="btn btn-secondary"
                        on:click={() => sendPassword()}>
                  Send
                </button>
              </div>
            </div>
          {:else}
            <div class="mt-10">
              <strong>Password: </strong>
              <div class="badge">{$store.password}</div>
            </div>
            <div class="font-size-12 text-muted">
              Share this password to let other people control the tracker.
            </div>
          {/if}
        {:else}
          <div class="badge badge-pill badge-danger">Not connected!</div>
        {/if}
      </div>
      <FairyClippy store={$store} />
    </div>
  </div>
</div>
