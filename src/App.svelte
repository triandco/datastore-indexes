<script lang="ts">
  import Verida from "@verida/datastore";
  import { onMount } from "svelte";

  const provider = "ethr";
  const applicationName = "VeridaDataStoreTest";
  let schema = "http://localhost:8080/schemata/animal/schema.json";
  type Animal = { name: string; id: string };
  let animalName: string = "Rover";
  let datastore;
  let animals: Animal[] = [];
  let userId: string;
  const pageSize: number = 2;
  let schemaContent: any;
  let bookmark: string | undefined = undefined;

  async function getDataStore(
    provider: "ethr" | "near",
    applicationName: string
  ) {
    const web3Provider = await Verida.Helpers.wallet.connectWeb3(provider);
    const address = await web3Provider.getAddress(provider);

    Verida.setConfig({ appName: applicationName });

    const app = new Verida({
      chain: provider,
      address: address,
      web3Provider: web3Provider,
    });

    await app.connect(true);
    userId = app.user.did;
    const datastore = await app.openDatastore(schema);
    return datastore;
  }

  async function loadAnimal() {
    const result = await datastore.getMany(
      {},
      { limit: pageSize, bookmark: bookmark, raw: true }
    );
    console.log(result);
    animals = result.docs;
    bookmark = result.bookmark;
    if(result.warning){alert(result.warning);}
  }

  async function addItemButtonClicked() {
    await datastore.save({
      name: animalName,
      id: `${animalName}-${new Date().toISOString()}`,
    });
  }
  async function initDataStore() {
    await getDataStore(provider, applicationName).then((d) => (datastore = d));
  }
  async function loadSchema(){
    fetch(schema).then(x => x.json()).then(x => schemaContent=x).catch(e => alert(e.toString()));
  }

  onMount(()=>{
    initDataStore();
    loadSchema();
  });
</script>

<main>
  {#if datastore !== undefined}
    <p>Helloo {userId}</p>
    <h1>Add an entry to datastore</h1>
    <input type="text" bind:value={animalName} />
    <button on:click={addItemButtonClicked}>Create</button>
    <hr />
    <h2>Fetch from datastore</h2>
    <button on:click={loadAnimal}>Fetch</button>
    <ul>
      {#each animals as animal}
        <li>{animal.name} - {animal.id}</li>
      {/each}
    </ul>
    <hr />
    <h2>Schema</h2>
    <div>
      <label for="schemaURL">Source</label>
      <input type="text" style="width: 100%" id="schemaURL" disabled bind:value={schema} />
      <label for="databaseConfig">Database props</label>
      <textarea style="width:100%; min-height:320px;">{JSON.stringify(schemaContent, null, 4)}</textarea>
      
    </div>
  {:else}
    <p>Initialise datastore...</p>
  {/if}
</main>
