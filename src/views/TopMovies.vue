
  <template>
    <div class="common-layout">
      <el-container>
        <el-header>
            <el-menu
                class="el-menu-demo"
                mode="horizontal"
                :ellipsis="false"
            >
                <div class="left-items">
                    <el-menu-item index="0"><img src="logo-trans.png" class="logo" /></el-menu-item>
                    <div class="flex-grow" />
                </div>
                <div class="right-items">
                    <el-select v-model="selectedGenreId" class="m-2" placeholder="Select">
                        <el-option
                          v-for="genre in genres"
                          :key="genre.id"
                          :label="genre.name"
                          :value="genre.id"
                        />
                    </el-select> 
                </div>
            </el-menu>
        </el-header>
        <el-main>
            <el-card class="top-rated-movies">
                <el-carousel :interval="2200" type="card" height="350px" autoplay>
                    <el-carousel-item v-for="movie in selectCarouselDisplay" :key="movie.id" @click="movieDetails(movie)">
                    <img :src="'https://image.tmdb.org/t/p/w500' + movie.poster_path" />
                    </el-carousel-item>
                </el-carousel>
                <h3 class="genre" v-text="selectedGenre"></h3>
            </el-card>
            <el-dialog v-model="showDialog" title="Movie Information">
              <p>this is test</p>
            </el-dialog>
        </el-main>
      </el-container>
    </div>
  </template>

  <script>
    import axios from 'axios';

    export default {
        data() {
            return {
                actionMovies: [],
                adventureMovies: [],
                animeMovies: [],
                comedyMovies: [],
                crimeMovies: [],
                genres: [],
                showDialog: false,
                selectedGenreId: 28,
                api_key: "86dbd4e4cda5e1699767a982ef7eaadf"
            }
        },
        computed: {
          selectedGenre() {
            const selectedGenres = this.genres.find(genre => genre.id === this.selectedGenreId);
            if(selectedGenres) {
              console.log(selectedGenres.id);
              return selectedGenres.name;
            }
            return 'No Data';
          },
          selectCarouselDisplay() {
            if(this.selectedGenreId == 28) {
              return this.actionMovies;
            }
            else if(this.selectedGenreId == 12) {
              return this.adventureMovies;
            }
            else if(this.selectedGenreId == 16) {
              return this.animeMovies;
            }
            else if(this.selectedGenreId == 35) {
              return this.comedyMovies;
            }
            else if(this.selectedGenreId == 80) {
              return this.crimeMovies;
            }

            return [];
          },
        },
        mounted() {
            this.getGenre();
            this.topActionMovie();
            this.topAdventureMovie();
            this.topAnimeMovie();
            this.topComedyMovie();
            this.topCrimeMovie();
        },
        methods: {
            async topActionMovie() {
                await axios.get(`https://api.themoviedb.org/3/discover/movie?api_key=${this.api_key}&with_genres=28&sort_by=popularity.desc`)
                        .then(response => {
                            this.actionMovies = response.data.results.slice(0, 10);
                            console.log("action", this.actionMovies);
                        });
            },
            async topAdventureMovie() {
              await axios.get(`https://api.themoviedb.org/3/discover/movie?api_key=${this.api_key}&with_genres=12&sort_by=popularity.desc`)
                        .then(response => {
                            this.adventureMovies = response.data.results.slice(0, 10);
                            console.log("adventure", this.adventureMovies);
                        });
            },
            async topAnimeMovie() {
              await axios.get(`https://api.themoviedb.org/3/discover/movie?api_key=${this.api_key}&with_genres=16&sort_by=popularity.desc`)
                        .then(response => {
                          this.animeMovies = response.data.results.slice(0, 10);
                          console.log("anime", this.animeMovies);
                        })
            },
            async topComedyMovie() {
              await axios.get(`https://api.themoviedb.org/3/discover/movie?api_key=${this.api_key}&with_genres=35&sort_by=popularity.desc`)
                        .then(response => {
                          this.comedyMovies = response.data.results.slice(0, 10);
                          console.log("comedy", this.comedyMovies);
                        })
            },
            async topCrimeMovie() {
              await axios.get(`https://api.themoviedb.org/3/discover/movie?api_key=${this.api_key}&with_genres=80&sort_by=popularity.desc`)
                        .then(response => {
                          this.crimeMovies = response.data.results.slice(0, 10);
                          console.log("crime", this.crimeMovies);
                        })
            },
            async getGenre() {
              await axios.get(`https://api.themoviedb.org/3/genre/movie/list?api_key=${this.api_key}`)
                        .then(response => {
                          const allGenres = response.data;
                          this.genres = allGenres.genres.slice(0, 5);
                          console.log("genres", this.genres); 
                        });
            },
            movieDetails(movie) {
              this.showDialog = true;
                console.log("movie", movie)
            },
        },
    }
  </script>
  <style scoped>
  .top-rated-movies {
    background-color: #2f3d53;
    margin: 0 80px;
  }
 h2{ 
    margin: 0px;
 }
  .el-main {
    margin: 0;
  }
  .el-menu {
    background-color: #121212;
    justify-content: space-between;
  }
  .el-menu-item {
    color: #ffffff;
  }
  .el-carousel__item {
    width: 560px;
  }
  .el-carousel__item h3 {
    color: #475669;
    opacity: 0.75;
    line-height: 200px;
    margin: 100px;
    text-align: center;
  }
  
  .el-carousel__item:nth-child(2n) {
    background-color: #99a9bf;
  }
  
  .el-carousel__item:nth-child(2n + 1) {
    background-color: #d3dce6;
  }
  img {
    width: 500px;
    height: 350px;
    display: block;
    margin: 0 auto;
  }

  .left-items {
    display: inline-flex;
  }
  .right-items {
    display: inline-flex;
    flex-wrap: wrap;
    align-content: center;
  }
  .genre {
    text-align: center;
    margin: 0;
    color: #ffffff
  }
  .logo {
    width: 180px;
    height: 50px;
  }
  </style>
  