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
      "mission_slug": "002",
      "current_md": "",
      "events": "",
      "missions": [
        {
          "slug": "001",
          "name": "a bridge too far",
          "status": "success"
        },
         {
          "slug": "002",
          "name": "Get Collard",
          "status": "start"
        },
      ],
      "pilots": [
        {
          "callsign": "Outrigger",
          "alias": "Pappy Bong",
          "code": "3d6ca0e0-e73d-4f0c-b161-09a28535bf3b//274e70ac-e5c2-4756-a7b4-6b76b7415b57",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Augenblick"
        },
        {
          "callsign": "Mouse",
          "alias": "Gerry Thomas",
          "code": "3d6ca0e0-e73d-4f0c-b161-09a28535bf3b//7c169109-15ea-4d65-8165-23a9994ff6ae",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Pea Shooter"
        },
        {
          "callsign": "Mako",
          "alias": "Raniero Kun",
          "code": "3D6CA0E0-E73D-4F0C-B161-09A28535BF3B//5861FD53-F2CC-4C6A-BA98-6036EEE5DA25",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Judgemental Connotation"
        },
        {
          "callsign": "Badgers",
          "alias": "Deke Rogers",
          "code": "3d6ca0e0-e73d-4f0c-b161-09a28535bf3b//adadc250-dd0d-4fd3-b340-da828f044bb8",
          "corpro": "GMS",
          "frame": "Ode to Truth",
          "mech": "Mayfly"
        },
        {
          "callsign": "Tweek",
          "alias": 'Clint "CC" Cruz',
          "code": "3d6ca0e0-e73d-4f0c-b161-09a28535bf3b//e43ead24-bb2b-4f61-9b32-4c895a15127e",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Monkey Wrench"
        },
      ],
      "header": {
        "planet": "Lambda 02",
        "year": "5014u",
        "system": "Aeon-3",
        "gate": "Aeon-Quanokrim",
        "ring": "Aon-Line",
        "headerTitle": "Fallen Star",
        "headerSubtitle": "Lancer Company",
        "subheaderTitle": "Assault Response",
        "subheaderSubtitle": "Capture-extraction",
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
