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
    Collapse,
  } from "sveltestrap";

  let result = "Waiting for your input above";
  let input_sent = "";
  let lang = "vi";
  let edit_url = false;

  let url = "https://0e0b-103-141-140-74.ap.ngrok.io";

  function submit_sentence() {
    fetch(`${url}/api`, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        text: input_sent,
        lang: lang,
      }),
    })
      .then((response) => response.json())
      .then((data) => {
        if (data.IsSuccessed) {
          result = data.ResultObj.result;
        } else {
          alert("There is an error");
        }
      });
  }
</script>

<Card>
  <CardHeader>
    <CardTitle>Unsupervised paraphrasing</CardTitle>
  </CardHeader>
  <CardBody>
    <Form>
      <form on:submit|preventDefault={submit_sentence}>
        <FormGroup>
          <Input type="switch" bind:checked={edit_url} label="Edit URL" />
        </FormGroup>
        <Collapse isOpen={edit_url}>
          <FormGroup>
            <Label for="url">Url</Label>
            <Input type="url" id="url" bind:value={url} />
          </FormGroup>
        </Collapse>
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
          <Input bind:value={lang} type="select">
            <option value="vi">Vietnamese</option>
            <option value="en">English</option>
          </Input>
        </FormGroup>
        <FormGroup>
          <Label for="exampleEmail">Result</Label>
          <Input type="text" bind:value={result} disabled />
        </FormGroup>
        <Button outline color="primary">Paraphrase</Button>
      </form>
    </Form>
  </CardBody>
</Card>
