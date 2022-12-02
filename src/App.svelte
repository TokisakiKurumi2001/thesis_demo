<script lang="ts">
  import {
    Form,
    FormGroup,
    Input,
    Label,
    Card,
    CardBody,
    CardTitle,
    CardHeader,
    Button,
  } from "sveltestrap";
  import { onMount } from "svelte";

  let result = "Waiting for your input above";
  let input_sent = "";
  let select_feature = "";
  let features = [];
  let original_sent = "";
  let pay_yet = 1;

  let url = "https://8117-103-141-140-74.ap.ngrok.io";
  onMount(() => {
    fetch(`${url}/features/`, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
    })
      .then((response) => response.json())
      .then((data) => (features = data.features));

    let last_day = new Date(2022, 8, 15).getTime();
    let today = new Date().getTime();
    // pay_yet = (last_day - today) / 1561603070;
    pay_yet = 1;
  });

  function submit_sentence() {
    // let select_feature = "detect_svo_v1";
    let sent_text = "";
    if (select_feature.startsWith("recommend")) {
      sent_text = input_sent + "$" + original_sent;
    } else {
      sent_text = input_sent;
    }
    fetch(`${url}/api/`, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        text: sent_text,
        feature: select_feature,
      }),
    })
      .then((response) => response.json())
      .then((data) => (result = data.ResultObj.result));
  }
</script>

<Card style="opacity: {pay_yet};">
  <CardHeader>
    <CardTitle>GrammarlyHCMUT Demo</CardTitle>
  </CardHeader>
  <CardBody>
    <Form>
      <form on:submit|preventDefault={submit_sentence}>
        <FormGroup>
          <Label for="something">Question to this answer</Label>
          <Input
            type="text"
            name="text"
            placeholder="Only use this feature with recommend_svo_v2. Don't use it else where since it's not wise to do so. (^_^)"
            bind:value={original_sent}
          />
        </FormGroup>
        <FormGroup>
          <Label for="exampleEmail">Sentence</Label>
          <Input
            type="text"
            name="text"
            placeholder="Input your text"
            bind:value={input_sent}
          />
        </FormGroup>
        <FormGroup>
          <Label for="exampleSelect">Choose your feature</Label>
          <Input bind:value={select_feature} type="select">
            {#each features as feature}
              <option value={feature}>{feature}</option>
            {/each}
          </Input>
        </FormGroup>
        <FormGroup>
          <Label for="exampleEmail">Result</Label>
          <Input type="text" bind:value={result} disabled />
        </FormGroup>
        <Button outline color="primary">Submit</Button>
      </form>
    </Form>
  </CardBody>
</Card>
