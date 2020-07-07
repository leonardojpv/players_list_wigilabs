<template>
<div>
  <div class="players-list" v-if="!detailsView">
    <div id="search-box">
        <input v-model="filter" placeholder="Search by name, last name or team" type="search">
    </div>
    <div class="player">
      <div class="player-header player_id"> ID </div>
      <div class="player-header"> Last name </div>
      <div class="player-header"> First name </div>
      <div class="player-header"> Team </div>
    </div>
    <div class="player" v-for="player in FilteredPlayers" :key="player.id" v-on:click="GetPlayerDetails(player.id)">
      <div class="player-data player_id">
            {{ player.id }}
      </div>
      <div class="player-data">
            {{ player.last_name }}
      </div>
      <div class="player-data">
            {{ player.first_name }}
      </div>      
      <div class="player-data">
            {{ player.team.full_name }}
      </div>
    </div>
  </div>

  <div class="player-details" v-if="detailsView">
    <div class="player">
      <div class="player-header"> ID </div>
      <div class="player-data"> {{ PlayerDetails.id }} </div>
    </div>
    <div class="player">
      <div class="player-header"> Last name </div>
      <div class="player-data" v-if="EditField!=='last_name'" v-on:click="EditField = 'last_name';"> {{ PlayerDetails.last_name }} </div>
      <input class="player-data" v-else v-model=PlayerDetails.last_name v-on:keyup.13="UpdatePlayer(PlayerDetails.id, 'last_name');"/>
    </div>
    <div class="player">
      <div class="player-header"> First name </div>
      <div class="player-data" v-if="EditField!=='first_name'" v-on:click="EditField = 'first_name';"> {{ PlayerDetails.first_name }} </div>
      <input class="player-data" v-else v-model=PlayerDetails.first_name v-on:keyup.13="UpdatePlayer(PlayerDetails.id, 'first_name');"/>
    </div>
    <div class="player">
      <div class="player-header"> Height </div>
      <div class="player-data"> <div v-if="PlayerDetails.height_feet"> {{ PlayerDetails.height_feet + "'" + PlayerDetails.height_inches }}</div><div v-else> {{ 'Unknown' }} </div> </div>
    </div>
    <div class="player">
      <div class="player-header"> Weight </div>
      <div class="player-data" v-if="EditField!=='weight_pounds'" v-on:click="EditField = 'weight_pounds';"> <div v-if="PlayerDetails.weight_pounds"> {{ PlayerDetails.weight_pounds }}</div><div v-else> {{ 'Unknown' }} </div> </div>
      <input class="player-data" v-else v-model=PlayerDetails.weight_pounds v-on:keyup.13="UpdatePlayer(PlayerDetails.id, 'weight_pounds');"/>
    </div>
    <div class="player">
      <div class="player-header"> Position </div>
      <div class="player-data" v-if="EditField!=='position'" v-on:click="EditField = 'position';"> {{ PlayerDetails.position }} </div>
      <input class="player-data" v-else v-model=PlayerDetails.position v-on:keyup.13="UpdatePlayer(PlayerDetails.id, 'position');"/>
    </div>
    <div class="player">
      <div class="player-header"> Team </div>
      <div class="player-data" v-if="EditField!=='team.full_name'" v-on:click="EditField = 'team.full_name';"> {{ PlayerDetails.team.full_name }} </div>
      <input class="player-data" v-else v-model=PlayerDetails.team.full_name v-on:keyup.13="UpdatePlayer(PlayerDetails.id, 'team.full_name');"/>
    </div>
    <div class="player">
      <div class="player-header"> City </div>
      <div class="player-data" v-if="EditField!=='team.city'" v-on:click="EditField = 'team.city';"> {{ PlayerDetails.team.city }} </div>
      <input class="player-data" v-else v-model=PlayerDetails.team.city v-on:keyup.13="UpdatePlayer(PlayerDetails.id, 'team.city');"/>
    </div>
    <div class="player">
      <div class="player-header"> Conference </div>
      <div class="player-data" v-if="EditField!=='team.conference'" v-on:click="EditField = 'team.conference';"> {{ PlayerDetails.team.conference }} </div>
      <input class="player-data" v-else v-model=PlayerDetails.team.conference v-on:keyup.13="UpdatePlayer(PlayerDetails.id, 'team.conference');"/>
    </div>
    <div class="player">
      <div class="player-header"> Division </div>
      <div class="player-data" v-if="EditField!=='team.division'" v-on:click="EditField = 'team.division';"> {{ PlayerDetails.team.division }} </div>
      <input class="player-data" v-else v-model=PlayerDetails.team.division v-on:keyup.13="UpdatePlayer(PlayerDetails.id, 'team.division');"/>
    </div>
    <div class="player closeDetails" v-on:click="CloseDetails">
      <div class="player-header"> Cerrar </div>
    </div>
  </div>

</div>
</template>

<script>
import axios from "axios";

export default {
  name: 'DataFetcher',
  data () {
    return {
      filter: '',
      players: [], 
      players_list: [],
      apiURL: 'https://www.balldontlie.io/api/v1/players/',
      detailsView: false,
      PlayerDetails: [],
      EditID: '',
      EditField: ''
    }
  },
  created () {
    axios.get(this.apiURL).then(response => (this.players = response.data, this.players_list = this.players.data));
  },
  computed: {
    FilteredPlayers: function (){
        return this.players_list.filter((item) => ( (item.last_name.toLowerCase().includes(this.filter.toLowerCase())) || (item.first_name.toLowerCase().includes(this.filter.toLowerCase()) || (item.team.name.toLowerCase().includes(this.filter.toLowerCase())))  ) );
    }
  },
  methods: {
    GetPlayerDetails: function (id) {
      for(const key in this.players_list){
        if(this.players_list[key].id === id){
          this.PlayerDetails = this.players_list[key];
          this.detailsView = true;
        }
      }
      
    },
    CloseDetails: function () {
      this.detailsView = false
    },
    UpdatePlayer: function (id, field) {
      for(const key in this.players_list){
        if(this.players_list[key].id === this.PlayerDetails.id){
          this.players_list[key][field] = this.PlayerDetails[field];
        }
      }
      this.EditField = '';
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}

.players-list, .player-details{
  width: 100%; display: flex; flex-direction: column; align-items: center; margin-bottom: 60px;
}

.players-list .player{
  width: 70%; display: flex;
}

.player-details .player{
  width: 50%; display: flex;
}

.players-list .player-header{
  margin: 4px 4px; background: #ccc; padding: 10px 20px; font-weight: bold; flex: 10;
}
.player-details .player-header{
  margin: 4px 4px; background: #ccc; padding: 10px 20px; font-weight: bold; flex: 2;
}

.players-list .player-data{
  margin: 3px 3px; background: #eee; padding: 10px 30px; flex: 10; text-align: left; cursor: pointer;
}

.player-details .player-data{
  margin: 4px 4px; background: #eee; padding: 10px 20px; flex: 10; text-align: left;
}

.player-details input.player-data{
  margin: 4px 4px; background: #eee; padding: 0px 20px; flex: 10; text-align: left;
}

.players-list .player-header.player_id{
  flex: 5%;
}

.players-list .player-data.player_id{
  flex: 3%;
}

input{
  height: 40px; width: 450px; border: 1px solid #aaa; padding: 0 12px; margin: 12px;
}

.closeDetails{
  cursor: pointer;  
}
</style>