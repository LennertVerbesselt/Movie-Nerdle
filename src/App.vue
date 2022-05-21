<template>
  <header class="header-container">
    <img @click="this.showInfo = true" src="./assets/help.svg" alt="" class="icon">
    <h1>Movie Nerdle</h1>
    <img  src="./assets/stats.svg" alt="" class="icon">
  </header>

  <ActorImages :Actor1="this.actor1" :Actor2="this.actor2" :Actor3="this.actor3" :Actor4="this.actor4" :Actor5="this.actor5" :Actor6="this.actor6" :Guess="this.guess"/>

  <div class="wrapper">
      <div id="search-input" class="search-input" @click="this.elasticSearch()">
        <a href="" target="_blank" hidden></a>
        <input type="text" v-model="inputFieldText" placeholder="Type to search...">
        <div class="autocom-box">
          <!-- here list are inserted from javascript -->
        </div>
        
      </div>
      <div class="buttons">
        <button @click="this.skipRound()" class="skip-button"> <span class="guess-text">Skip</span></button>
        <button @click="this.makeGuess()" class="guess-button"> <span class="guess-text">Guess</span></button>
        
      </div>
    </div>


    <div v-if="this.showPopup" class="popup-container">
      <div  class="popup-window">
        <div class="popup-title">{{this.popupTitle}}</div>
        <div class="movie-poster">
          <img  :src="'https://image.tmdb.org/t/p/w500/' + this.movie_poster" alt="" class="">
        </div>
        <div class="movie-title">{{this.movie_st}}</div>
        <button @click="this.nextRound()" class="next-button"> <span class="guess-text">Next</span></button>
      </div>
    </div>

    <div v-if="this.showInfo" @click="this.showInfo = false" class="popup-container">
      <div  class="popup-window">

        <div class="how-to-play">
          <h3>How to play:</h3>
          <p>Try and guess the movie that these actors acted in together in as few guesses possible. (Tip: if your movie is not in the auto-complete, then it's not going to be correct 😉)</p>
        </div>
        <div class="created-by">
          <h5>CREATED BY</h5>
          <img class="picture-lennert" src="./assets/JAM_x_TOAST_Personal_picture_Lennert.jpg" alt="">
            <p>Lennert Verbesselt</p>
          <a href="https://www.buymeacoffee.com/lennert"><img src="https://img.buymeacoffee.com/button-api/?text=Buy me a coffee&emoji=☕&slug=lennert&button_colour=FFDD00&font_colour=000000&font_family=Poppins&outline_colour=000000&coffee_colour=ffffff" /></a>
        </div>
        <div class="powered-by">
          <h5>POWERED BY</h5>
          <img   src="./assets/The_Movie_DB_Logo_Vector_long.svg" alt="" class="">
        </div>
        
        <button @click="this.showInfo = false" class="next-button"> <span class="guess-text">Play</span></button>
      </div>
    </div>
    
</template>

<script>

import axios from 'axios';
import ActorImages from './components/ActorImages.vue'
import jsonDB from './/db.json'
import eDB from './/elasticDB.json'

export default {
  name: 'App',
  components: {
    ActorImages,
  },
  data () {
    return {
      BASEURL: "https://api.themoviedb.org/3/",
      POPULARURL : "https://api.themoviedb.org/3/movie/top_rated?api_key=",
      // CREDITSURL: "https://api.themoviedb.org/3/movie/{movie_id}/credits?api_key=",
      APIKEY: "e4f433a66e1ab61155c05fe8c97ca63a",
      db: [],
      iterations: 200,

      db2: {},

      jsondb: jsonDB,

      ACTIVEMOVIE: 0,
      movie_title: "",
      movie_id: 0,
      movie_poster: "",
      movie_rating: 0,
      movie_year: 0,
      movie_st : "",
      movie_director: "",
      actor1: {},
      actor2: {},
      actor3: {},
      actor4: {},
      actor5: {},
      actor6: {},

      guess: 0,

      inputFieldText: "",

      message: "",

      stats: {
        total: 0,
        one: 0,
        two: 0,
        three: 0,
        four: 0,
        five: 0,
        failed: 0,
      },

      showPopup: false,
      popupTitle: "",

      showInfo: false,

    }
  },
  methods: {

    getActiveMovieData(){
      //console.log(jsonDB);
      let movie = JSON.parse(JSON.stringify(jsonDB))[this.ACTIVEMOVIE];
      this.movie_title = movie.original_title;
      this.movie_id = movie.id;
      this.movie_poster = movie.poster_path;
      this.movie_rating = movie.rating;
      this.movie_year = movie.release_date;
      this.movie_st = movie.searchTerm;
      this.movie_director = movie;
      this.actor1 = movie.cast[0];
      this.actor2 = movie.cast[1];
      this.actor3 = movie.cast[2];
      this.actor4 = movie.cast[3];
      this.actor5 = movie.cast[4];
      this.actor6 = movie.cast[5];
      
    },

    elasticSearch() {
      const searchWrapper = document.querySelector(".search-input");
      const inputBox = searchWrapper.querySelector("input");
      const suggBox = searchWrapper.querySelector(".autocom-box");

      
      //console.log(localStorage.elasticDB);

      let edb = JSON.parse(JSON.stringify(eDB));
      
      let database = [].concat(edb);
      //console.log(database);

      inputBox.onkeyup = (e)=>{
        let userData = e.target.value;
        let emptyArray = [];
        if(userData) {
          emptyArray = database.filter((data)=>{
            return data.toLocaleLowerCase().includes(userData.toLocaleLowerCase());
          });
          emptyArray = emptyArray.map((data)=>{
            return data = '<li class="li-suggestions" id="' + data + '">' + data + '</li>';
          });
          //console.log(emptyArray);
          searchWrapper.classList.add("active");
          this.showSuggestions(emptyArray);

          document.querySelectorAll('.li-suggestions').forEach(item => {
            item.addEventListener('click', event => {
              this.inputFieldText = item.childNodes[0].nodeValue;
              searchWrapper.classList.remove("active");
            });
          });

          // let allList = suggBox.querySelectorAll("li");
          //console.log(allList);
          // for(let i = 0; i < allList.length; i++){
            //console.log(allList[i].childNodes[0].nodeValue);
            // allList[i].addEventListener("click", this.selectOption(allList[i].childNodes[0]));
            // allList[i].addEventListener("onclick", function() {
            //   console.log(allList[i].childNodes[0].nodeValue);
            //   this.inputFieldText = allList[i].childNodes[0].nodeValue;
            // });
          // }

        } else {
          searchWrapper.classList.remove("active");
        }
        
      }
    },

    

    showSuggestions(list){
      const searchWrapper = document.querySelector(".search-input");
      const inputBox = searchWrapper.querySelector("input");
      const suggBox = searchWrapper.querySelector(".autocom-box");

      let listData;
      if(!list.length) {
        let userValue = inputBox.value;
        listData = '<li> ' + userValue + '</li>';

      } else {
        listData = list.join('');
      }

      suggBox.innerHTML = listData;
    },


    async getPopular(page) {
      let url = ''.concat(this.POPULARURL, this.APIKEY, '&language=en-US&page=', page);
      await axios.get(url).then(response => {
                //console.log(response.data);

                this.db = this.db.concat(response.data.results);
                let data = JSON.stringify(this.db);
                window.localStorage.setItem('db', data);
            }).catch(error => {
                console.log(error.response);
                
            });
    },

    async getNeededData(){
      let elasticDB = [];
      let e = 0;

      for(let i = 0; i < this.db.length; i++){

        let year = parseInt(this.db[i].release_date.substring(0, 4));

        if(this.db[i].original_language == "en" && this.db[i].release_date && this.db[i].original_title && year >= 1980 && this.db[i].popularity >= 25 && !this.db[i].genre_ids.includes(16)){

          let st = ''.concat(this.db[i].original_title, ' (', this.db[i].release_date.substring(0, 4), ')');

          this.db2[e] = {
            id: this.db[i].id,
            original_title: this.db[i].original_title,
            release_date: this.db[i].release_date,
            rating: this.db[i].vote_average,
            poster_path: this.db[i].poster_path,
            searchTerm : st,
          };

          

          elasticDB.push(''.concat(this.db[i].original_title, ' (', this.db[i].release_date.substring(0, 4), ')'));

          let url = ''.concat(this.BASEURL, 'movie/', this.db[i].id, '/credits?api_key=', this.APIKEY, '&language=en-US');

          await axios.get(url).then(response => {
                //console.log(response.data);
                // response.data.cast.sort((firstItem, secondItem) =>  secondItem.popularity - firstItem.popularity);
                this.db2[e]['cast'] = response.data.cast.slice(0, 7);
                
            }).catch(error => {
                console.log(error.response);
                
            });
            e++;
        }
      }
      //console.log(this.db2);
      
      let data = JSON.stringify(this.db2);
      window.localStorage.setItem('db2', data);

      let edata = JSON.stringify(elasticDB);
      window.localStorage.setItem('elasticDB', edata);
    },

    pickActiveMovie(){
      //console.log(eDB);
      this.ACTIVEMOVIE = Math.floor(Math.random()*JSON.stringify(eDB).length);
      if(!JSON.parse(JSON.stringify(jsonDB))[this.ACTIVEMOVIE]) {
        this.pickActiveMovie();
      } 
    },

    makeGuess() {
      if(this.inputFieldText == this.movie_st){
        this.updateStats();
        this.popupTitle = "Correct, it was";
        this.showPopup = true;
      } else {
        this.inputFieldText = "";
        this.guess++;

        if(this.guess == 5) {
          this.updateStats();
          this.message = "Wrong, it was actually";
          this.showPopup = true;
        }
      }
    },

    skipRound() {
      this.guess++;
      if(this.guess >= 5) {
        this.updateStats();
        this.popupTitle = "FYI, it was actually ";
        this.showPopup = true;
      }
    },

    loadStats(){
      if(localStorage.stats){
        this.stats = JSON.parse(localStorage.stats);
      } else {
        localStorage.stats = JSON.stringify(this.stats);
      }
    },

    updateStats() {
      this.stats = JSON.parse(localStorage.stats);

      this.stats.total += 1;

      if(this.guess == 0){
        this.stats.one += 1;
      } else if(this.guess == 1) {
        this.stats.two += 1;
      } else if(this.guess == 2) {
        this.stats.three += 1;
      } else if(this.guess == 3) {
        this.stats.four += 1;
      } else if(this.guess == 4) {
        this.stats.five += 1;
      } else {
        this.stats.failed += 1;
      }

      window.localStorage.setItem('stats', JSON.stringify(this.stats));
    },

    nextRound() {
          
          this.inputFieldText = "";
          this.pickActiveMovie();
          this.getActiveMovieData();
          this.guess = 0;
          this.showPopup = false;
    },

  },
  watch: {
    //Run to create initial Database
    //
    // db: function() {
    //   if(this.db.length > 3977){
    //     this.getNeededData();
    //   }
    // }
  },

  created() {


    //Run to create initial Database
    //
    // for(let i = 1; i< this.iterations; i++) {
    //   this.getPopular(i);
    // }

    this.pickActiveMovie();
    this.getActiveMovieData();
    this.loadStats();
    
    
    
    
  },
  mounted() {
    //this.elasticSearch();
  }
}
</script>

<style>

body {
  background-color: #121213;
  color: #ffffff;
  font-family: Helvetica, Arial, sans-serif;
  overflow-x: hidden;

  padding: 0;
  margin: 0;
}

.header-container {
  display: flex;
  justify-content: space-between;
  flex-direction: left;
  align-items: center;
  width: 99vw;
  height: 60px;
  margin: auto;

  border-bottom: 1px solid #3a3a3c;
}

h1 {
  font-size: 22px;
}

.icon {
  width: 20px;
  height: auto;
  padding-left: 1rem;
  padding-right: 1rem;
}

.wrapper{
  width: 80vw;
  max-width: 600px;
  margin: auto;
}
.wrapper .search-input{
  background: #fff;
  width: 100%;
  border-radius: 6px;
  position: relative;
}
.search-input input{
  height: 55px;
  width: 90%;
  margin: auto;
  outline: none;
  border: none;
  border-radius: 6px;
  font-size: 18px;
  padding: 0px 0px 0px 15px
  
}
.search-input.active input{
  border-radius: 5px 5px 0 0;
}
.search-input .autocom-box{
  padding: 0;
  opacity: 0;
  pointer-events: none;
  max-height: 180px;
  overflow-y: auto;
}
.search-input.active .autocom-box{
  opacity: 1;
  pointer-events: auto;
  color: #121213;
}
.autocom-box li{
  list-style: none;
  display: none;
  padding: 8px 12px;
  cursor: default;
  border-radius: 3px;
  color: #121213;
  font-size: 18px;
}
.search-input.active .autocom-box li{
  display: block;
}
.autocom-box li:hover{
  background: #efefef;
}

.buttons {
  width: 80vw;
  max-width: 600px;
  height: 55px;
  margin-top: 1rem;

  display: flex;
  justify-content: space-between;
  align-items: center;
}

.guess-button {
  width: 48%;
  height: 55px;
  background-color: #48cd88;
  border-radius: 6px;
  outline: none;
  border: none;
}

.skip-button {
  width: 48%;
  height: 55px;
  background-color: #f95a6f;
  border-radius: 6px;
  outline: none;
  border: none;
}

.guess-text {
  color: #ffffff;
  font-family: Helvetica, Arial, sans-serif;
  font-size: 20px;
  font-weight: 700;

}

.info-message {
  width: 60vw;
  margin: auto;
  max-width: 400px;

  font-family: Helvetica, Arial, sans-serif;
  font-size: 14px;
  font-weight: 700;
  text-align: center;

  padding-top: 1rem;
  padding-bottom: 5rem;

}

.popup-container {
  position: absolute;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;

  font-family: Helvetica, Arial, sans-serif;
  font-size: 22px;
  font-weight: 700;


  background-color: rgba(0, 0, 0, .4);
}

.popup-window {
  width: 70vw;
  max-width: 800px;
  margin: auto;
  margin-top: 5rem;
  border-radius: 6px;

  background-color: #121213;

  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  
}

.popup-title {
  font-family: Helvetica, Arial, sans-serif;
  font-size: 28px;
  font-weight: 700;

  padding-top: 1rem;
  padding-bottom: 1rem;
  margin-left: 2rem;
  margin-right: 2rem;
  text-align: center;
}

.movie-poster img {
  width: 40vw;
  max-width: 200px;
  border-radius: 6px;
  margin-top: 1rem;
  margin-bottom: 1rem;
}

.movie-title {
  font-size: 16px;
  text-align: center;
  margin-bottom: 2rem;
}

.next-button {
  width: 80%;
  height: 55px;
  background-color: #48cd88;
  border-radius: 6px;
  outline: none;
  border: none;
  margin-bottom: 2rem;
}

.how-to-play {
  width: 80%;
  margin: auto;
  max-width: 600px;

  text-align: center;
}

.how-to-play p {
  font-size: 14px;
  font-weight: 300;
  max-width: 300px;
  margin: auto;
  
}

.created-by {
  text-align: center;
  margin-bottom: 2rem;
}

.created-by h5 {
  font-size: 14px;
}

.created-by p {
  font-size: 16px;;
}

.picture-lennert {
  width: 20%;
  border-radius: 50%;
  max-width: 200px;
}

.created-by a img {
  width: 70%;
  max-width: 400px;
}

.powered-by {
  text-align: center;
}

.powered-by h5 {
  font-size: 14px;
}

.powered-by img {
  width: 70%;
  margin-bottom: 2rem;
}
</style>
