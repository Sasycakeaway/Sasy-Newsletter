<script>
  import {
    Page,
    Navbar,
    NavTitle,
    NavTitleLarge,
    Block,
    ListInput,
    Icon,
    List,
    Button,
    f7
  } from "framework7-svelte";
  import emailjs from '@emailjs/browser';

  let pass = "",
    msg = "",
    subject = "";

  async function Get_Users() {
    var myHeaders = new Headers();
    myHeaders.append("Content-Type", "application/x-www-form-urlencoded");

    var urlencoded = new URLSearchParams();
    urlencoded.append("password", pass);

    var requestOptions = {
      method: 'POST',
      headers: myHeaders,
      body: urlencoded,
      redirect: 'follow'
    };

    let raw_users = await fetch("http://127.0.0.1:5004/api/newsletter", requestOptions);
    try {
      return await raw_users.json();
    } catch (error) {
      console.error(error);
      return [];
    }
  }
  async function send() {
    let users = await Get_Users();
    if(users == "0"){
      f7.dialog.alert("Password errata");
      users = [];
    }
    try {
      emailjs.send("service_9w2mopi","template_zbv3q8p",{
        message: msg,
        emails: users
      });
      f7.dialog.alert("Email inviata correttamente");
    } catch (error) {
      console.error(error);
      f7.dialog.alert("Errore durante l'invio dell'email");
    }

  }
</script>

<Page name="home">
  <!-- Top Navbar -->
  <Navbar large sliding={false}>
    <NavTitle sliding>Sasy's Newsletter</NavTitle>
    <NavTitleLarge>Sasy's Newsletter</NavTitleLarge>
  </Navbar>
  <!-- Page content -->
  <Block strong>
    <p>Benvenuto/a nell'app di Sasy's Cake Away per gestire la newsletter</p>
  </Block>

  <Block>
    <List>
      <ListInput
        label="Password"
        type="password"
        placeholder="Password di accesso"
        clearButton
        bind:value={pass}
      >
        <Icon f7="lock" slot="media" />
      </ListInput>
      <ListInput
      label="Oggetto"
      type="password"
      placeholder="Oggetto della mail"
      clearButton
      bind:value={subject}
      >
        <Icon f7="doc" slot="media" />
      </ListInput>
      <ListInput
        label="Messaggio da inviare"
        type="textarea"
        placeholder="Il tuo messaggio"
        bind:value={msg}
      >
        <Icon f7="doc" slot="media" />
      </ListInput>
      <div class="btn">
        <Button fill on:click={send}>Invia il messaggio</Button>
      </div>
    </List>
  </Block>
</Page>

<style>
  .btn {
    padding: 30px;
  }
</style>
