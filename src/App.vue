<template>
  <Header :header="this.header" />
  <div class="content-container">
    <section class="section-container" id="missions" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/mission-icon.svg" />
        <h1>Mission Log</h1>
      </div>
      <div class="section-content-container">
        <h3>Current Assignment</h3>
        <Markdown :source="current_md" class="markdown" />
        <h3>Mission List</h3>
        <div class="mission-list-container">
          <Mission
            v-for="item in this.missions"
            :key="item.slug"
            :mission="item"
            :selected="this.mission_slug"
            @click="selectMission(item)"
          />
        </div>
      </div>
    </section>
    <section class="section-container" id="events" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/events-icon.svg" />
        <h1>Events Log</h1>
      </div>
      <div class="section-content-container">
        <Markdown :source="events" class="markdown" />
      </div>
    </section>
    <section class="section-container" id="pilots" style="width:894px; height:714px;">
      <div style="height:52px; overflow:hidden;">
        <div class="section-header clipped-medium-backward-pilot">
          <img src="/icons/pilot-icon.svg" />
          <h1>Pilot Roster</h1>
        </div>
        <div class="rhombus-back">&nbsp;</div>
      </div>
      <div class="section-content-container">
        <div class="pilot-list-container">
          <Pilot v-for="item in this.pilots" :key="item.slug" :pilot="item" />
        </div>
      </div>
    </section>
  </div>
  <svg
    style="visibility: hidden; position: absolute;"
    width="0"
    height="0"
    xmlns="http://www.w3.org/2000/svg"
    version="1.1"
  >
    <defs>
      <filter id="round">
        <feGaussianBlur in="SourceGraphic" stdDeviation="5" result="blur" />
        <feColorMatrix
          in="blur"
          mode="matrix"
          values="1 0 0 0 0  0 1 0 0 0  0 0 1 0 0  0 0 0 19 -5"
          result="goo"
        />
        <feComposite in="SourceGraphic" in2="goo" operator="atop" />
      </filter>
    </defs>
  </svg>
  <audio autoplay>
    <source src="/startup.ogg" type="audio/ogg" />
  </audio>
  <Footer/>
</template>

<script>
import Header from './components/layout/Header.vue';
import Footer from './components/layout/Footer.vue';
import Mission from './components/Mission.vue';
import Pilot from './components/Pilot.vue';
import Markdown from 'vue3-markdown-it';

export default {
  components: {
    Header,
    Footer,
    Mission,
    Pilot,
    Markdown
  },

  data() {
    return {
      "mission_slug": "001",
      "current_md": "",
      "events": "",
      "missions": [
        {
          "slug": "001",
          "name": "Drawdown 5016u",
          "status": "start"
        },
        {
          "slug": "002",
          "name": "Collateral",
          "status": "start"
        },
        {
          "slug": "003",
          "name": "JUNO",
          "status": "start"
        },
      ],
      "pilots": [
        {
          "callsign": "Dispatch",
          "alias": "Lucy Beltayn",
          "code": "Player: Cozmicc",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Radiant Flower Questions the Dead"
        },
        {
          "callsign": "Ouroboros",
          "alias": "Nadija Kaemmerer-Abbas",
          "code": "Player: Starvolt",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "THRICE FOLDED, I HAVE GAZED UPON THE FRAMEWORK OF REALITY ITSELF"
        },
        {
          "callsign": "Event Horizon",
          "alias": "Dalt Stirad",
          "code": "Player: Dusty",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Perfect Vacuum"
        },
        {
          "callsign": "Magpie",
          "alias": "Alexandria Rowland",
          "code": "Player: Alex",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Magpie"
        },
        {
          "callsign": "Exile",
          "alias": "Suldred Khayradi IV",
          "code": "Player: Disco",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "TBA"
        },
      ],
      "header": {
        "planet": "Tyrno",
        "year": "5016u",
        "system": "Archimedes-4",
        "gate": "Liana-Hymn",
        "ring": "Liana-Line",
        "headerTitle": "Blacklight Direct",
        "headerSubtitle": "'Deeds in the Dark'",
        "subheaderTitle": "Conflict Resolution",
        "subheaderSubtitle": "Delta-Echo-Echo-Delta",
      },
      "options":{
        "eventsMarkdownPerMission": true
      }
    }
  },

  created() {
    this.loadMissionMarkdown()
    this.loadEventsMarkdown()
  },

  computed: {

  },

  methods: {
    selectMission(mission) {
      this.mission_slug = mission.slug;
      this.loadMissionMarkdown()
      if(this.options.eventsMarkdownPerMission){
        this.loadEventsMarkdown();
      }
    },
    loadMissionMarkdown() {
      let self = this;
      let md = `/missions/${self.mission_slug}.md`
      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.current_md = client.responseText;
      }
      client.send();
    },
    loadEventsMarkdown() {
      let self = this;
      let md = "";

      if(self.options.eventsMarkdownPerMission){
        md = `/events/${self.mission_slug}.md`
      }
      else {
        md = "/events.md"
      }

      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.events = client.responseText;
      }
      client.send();
    }
  }

}
</script>


<style lang="scss">
#app {
  width: 1902px;
  height: 910px;
  overflow: hidden;
}
</style>
