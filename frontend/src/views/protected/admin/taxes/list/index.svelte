<script>
  import Loading from "../../../../components/loading.svelte";
  import Alert from "../../../../components/alert/index.svelte";
  import TaxResults from "./results.svelte";
  import { TaxModel } from "../../../../../lib/models/tax";

  export let graphqlClient;

  const listParams = {};

  const taxesList = TaxModel().find(graphqlClient, listParams);

  function deleteTax(event) {
    TaxModel()
      .remove(graphqlClient, event.detail)
      .then(function(result) {
        if (result.errors.length === 0) {
          taxesList.refetch();
        }
      });
  }
</script>

{#await $taxesList}
  <Loading />
{:then result}
  {#if result.data.taxes.length > 0}
    <TaxResults taxes={result.data.taxes} on:deleteTax={deleteTax} />
  {:else}
    <Alert
      message="There are no discounts"
      extraInfo="You can be the lucky one that creates the first discount ever!" />
  {/if}
{:catch error}
  Error: {error}
{/await}
