<template>
  <v-app>
    <v-toolbar app dark>
      <v-toolbar-title class="headline text-uppercase">
        <span>Acceptance</span> &nbsp;
        <span class="font-weight-light">ASPNETCORE/VUE DEMO</span>
      </v-toolbar-title>
    </v-toolbar>
    <v-content>    
      <!-- Progress bar component -->
      <ProgressBar/>
      <!-- Alerts bar component -->
      <Alerts/>

      <HelloWorld/>
    </v-content>
    <v-footer dark color="primary">
      <v-flex primary text-xs-center white--text> &copy; {{ new Date().getFullYear() }} Acceptance Insurance Company </v-flex>
    </v-footer>
  </v-app>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';

import HelloWorld from './components/HelloWorld.vue'
import Alerts from './components/Alerts.vue';
import alertMixin  from './shared/alert-mixin'
import progressBarMixin  from './shared/progress-bar-mixin';
import ProgressBar from './components/ProgressBar.vue';
import DataServices from './services/data-services';

@Component({  
  components: {
    HelloWorld,
    Alerts,
    ProgressBar,
  }
})

export default class App extends Vue {
  constructor() {
    super();
    
  }

  mounted() {
    const urlParams = new URLSearchParams(window.location.search);
    const sessionId = urlParams.get('sessionId');
    
    if (sessionId === null) {    
      console.log('SessionId is null')
      alertMixin.error('Something is wrong with the SessionId.');
      
      return;
    }

    progressBarMixin.toggle();

    DataServices.getUserSessionData(sessionId)
      .then((result) => {
        console.log(JSON.stringify(result));
        alertMixin.success('Your sessionId is valid');
      })
      .catch((err) => {
        alertMixin.error(`Dataservice Error: ${err.name} - ${err.message}`); 
      })
      .finally(() => {
        progressBarMixin.toggle();
      });
  }
}
</script>
