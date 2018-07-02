<template>
<section>
  <section class="scores">
  <h1 class="header">
    {{results.MatchTime}}
    <br />
    <small class="time">{{last_retrieved.toTimeString()}}</small>
  </h1>
  <section>
    <Team :team="home_team" :penalty_score="results.HomeTeamPenaltyScore"></Team>
    <Team :team="away_team" :penalty_score="results.AwayTeamPenaltyScore"></Team>
  </section>
</section>
  <h1 class="round-name" v-if="results.StageName">{{ results.StageName[0].Description }}</h1>
</section>
</template> 

<script>
const API = 'https://api.fifa.com/api/v1/live/football/17/254645/275093/300331535?language=en-GB'
import Team from './Team.vue';
import goal_sound from './goal.mp3';

export default {
  components: {
    Team
  },
  data: () => ({
    last_retrieved: new Date(),
    results: {},
    home_team: {},
    away_team: {},
    players: {},
    first: true,
  }),
  mounted() {
    this.getScores();
  },
  methods: {
    getScores() {
      fetch(API)
      .then(res => res.json())
      .then(results => {

        if(!this.first && results.HomeTeam.Score != this.home_team.Score
          || results.AwayTeam.Score != this.away_team.Score
          || results.HomeTeamPenaltyScore != this.HomeTeamPenaltyScore
          || results.AwayTeamPenaltyScore != this.AwayTeamPenaltyScore) {
          const audio = new Audio(goal_sound);
          audio.volume = 0.2;
        }
        console.log(results)
        this.last_retrieved = new Date();
        this.results = results;
        this.home_team = results.HomeTeam;
        this.away_team = results.AwayTeam;
        this.first = false;
        setTimeout(() => {
          this.getScores()
        }, 5000)
      });
    }
  }
}
</script>

<style>
body {
  text-align: center;
  font-family: sans-serif;
  font-size: 3em;
}

.header {
  padding: 0;
}

.round-name {
  font-size: 1em;
  margin: 0;
}

.scores {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
  align-items: center;
}

.time {
  font-size: 0.2em;
}

</style>
