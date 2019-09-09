<template>
    <div class="quotes">
        <button id="btn"  v-on:click="getQuotes">Get Quotes</button>
        
        <div class="filter">
            <label><input type="radio" v-model="selectedCategory" value="all" /> All</label>
            <label><input type="radio" v-model="selectedCategory" value="movies" /> MOVIES</label>
            <label><input type="radio" v-model="selectedCategory" value="games" /> GAMES</label>
        </div>

        <div>
            <button @click="prevPage" :disabled="pageNumber <= 0" v-if="selectedCategory==='all'">Previous</button>
            <button @click="nextPage" :disabled="pageNumber >=  numPage -1" v-if="selectedCategory==='all'">Next</button> 
            <button @click="prevMovieP" :disabled="pageNumberMovie <= 0" v-if="selectedCategory==='movies'">Previous</button>
            <button @click="nextMovieP" :disabled="pageNumberMovie >= numPageMovie -1" v-if="selectedCategory==='movies'">Next</button>     
            <button @click="prevGameP" :disabled="pageNumberGame <= 0 " v-if="selectedCategory==='games'">Previous</button>
        </div>

        <main>
            <div class="filterByCategory">
              <input type="text" v-model="search" placeholder="search quotes" v-if="selectedCategory==='all'">
            </div>

            <div class="wrapper">
                <div class="row">
                    <table class="table" >
                        <tr>
                            <th>Context</th>
                            <th>Quote</th>
                            <th>Source</th>
                            <th>Theme</th>
                        </tr>
                        <tr v-for="quote in filteredTheme " :key="quote.id">
                            <td>  {{ quote.context }} </td>
                            <td>{{ quote.quote }} </td>
                            <td>{{ quote.source }} </td>
                            <td >{{ quote.theme }} </td>
                        </tr>
                    </table>        
                </div>
            </div>
        </main>
    </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'Quotes',
  data() {
    return {
      quotes: [],
      paginatedQuotes: [],
      paginatedMovies: [],
      paginatedGames: [],
      loading: false,
      pageNumber: 0,
      pageNumberMovie: 0,
      pageNumberGame: 0,
      numPage: 0,
      numPageMovie: 0,
      numPageGame: 0,
      perPage: 15,
      search:'',
      selectedCategory: "all",
    }
  },
  methods: {
    getQuotes: function () {
      this.loading = true;
      axios.get("https://gist.githubusercontent.com/benchprep/dffc3bffa9704626aa8832a3b4de5b27/raw/quotes.json")
      .then((response)  =>  {
        this.loading = false;
        this.quotes = response.data;
      }, (error)  =>  {
        this.loading = false;
      })
    },
    nextPage(){
        this.pageNumber++;
    },
    prevPage(){
        this.pageNumber--;
    },
    nextMovieP(){
        this.pageNumberMovie++;
    },
    prevMovieP(){
        this.pageNumberMovie--;
    },
    nextGameP(){
        this.pageNumberGame++;
    },
    prevGameP(){
        this.pageNumberGame--;
    },
  },
  computed:{
      filteredTheme: function(){
        let quotesFiltered = this.quotes;
        let self = this;
        let category = this.selectedCategory;
        if( category === "all"){
              let start = self.pageNumber * self.perPage,
              end = start + self.perPage;
            // ("end:"+ end); 
              let numPaginatedQuotes = quotesFiltered.slice(start, end);
              self.paginatedQuotes = numPaginatedQuotes;
              let l = quotesFiltered.length;
              self.numPage = Math.ceil(l/this.perPage);
            return self.paginatedQuotes.filter((data) =>{
              return data;
            });
            return self.numPage;
          }
        if(category === "movies"){
              let movieCategory = "movies";
              let  startMovie = self.pageNumberMovie * self.perPage, 
              endMovie = startMovie + self.perPage;
               self.paginatedMovies = quotesFiltered.filter((data) =>{
                  return data.theme === category;
                });
                let ml = self.paginatedMovies.length;
                self.numPageMovie = Math.ceil(ml/this.perPage);
                let numPagninatedMovies = self.paginatedMovies.slice(startMovie, endMovie);
                self.paginatedMovies = numPagninatedMovies;
              return self.paginatedMovies.filter((data) =>{
                return data.quote;
              });  
        }
        if(category === "games"){
              let gameCategory = "games";
              let  startGame = self.pageNumberGame * self.perPage, 
              endGame = startGame + self.perPage; 
              self.paginatedGames = quotesFiltered.filter((data) =>{
                  return data.theme === category;
                });
                self.paginatedGames.slice(startGame, endGame);                
                let gl = self.paginatedGames.length;
                self.numPageGame= Math.ceil(gl/this.perPage);
              return self.paginatedGames.filter((data) =>{
                return data;
              });  
              return self.numPageGame;
        }
         else {
            const start = this.pageNumber * this.perPage, 
              end = start + this.perPage;
              this.paginatedGames = quotesFiltered.slice(start, end);
            return this.paginatedGames;
        }
          return quotesFiltered.filter((data) => {
            return data.quote.toLowerCase().match(this.search.toLowerCase());
            // console.log(paginatedQuote.quote);
            // return data.quote;
          });
        // }
        //  else{
        //   return this.paginatedQuotes.filter((paginatedQuote) =>{
        //     // console.log(paginatedQuote.theme);
        //     return paginatedQuote.theme === category;
        //   });
        // }
      },
    
  },

}
</script>

<style scoped>
* {
  margin: 5px;
}
h3 {
  margin: 40px 0 0;
}
.table  {
width: 100%
}
th{
    width: 300px;
}
td{
    height: 60px;
    margin: 5%;
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
</style>
