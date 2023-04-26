
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
              <h3>Top Movies</h3>
                <el-carousel :interval="222200" type="card" height="400px" autoplay>
                    <el-carousel-item v-for="movie in selectCarouselDisplay" :key="movie.id" @click="movieDetails(movie)">
                    <img :src="'https://image.tmdb.org/t/p/w500' + movie.poster_path" />
                    </el-carousel-item>
                </el-carousel>
                <h3 class="genre" v-text="selectedGenre"></h3>
            </el-card>
            <h3 class="recommended">Recommended Movies</h3>
            <el-row>
              <el-col v-for="(recom) in recommendedMovies" :key="recom.id" :span="4" @click="movieDetails(recom)">
                <el-card style="width: 100%; margin: 10px;">
                  <div style="display: flex; justify-content: center; align-items: center; height: 300px;">
                    <img style="max-width: 100%; max-height: 100%; cursor: pointer" :src="'https://image.tmdb.org/t/p/w500' + recom.poster_path" />
                  </div>
                </el-card>
              </el-col>
            </el-row>
            <el-dialog v-model="showDialog" title="Movie Information">
              <div class="image-container">
                <div class="image-wrapper">
                  <img class="image-info" :src="'https://image.tmdb.org/t/p/w500' + selectedMovie.poster_path" />
                  
                </div>
                <div class="info-container">
                  <p style="font-weight: bold; color: black; font-size: 25px;margin-bottom:0px;text-decoration:underline">{{selectedMovie.title}}</p>
                  <p class="movie-rating" style="margin-top:1px;color:#67C23A;">Movie Rating: {{selectedMovie.vote_average.toFixed(2)}}⭐</p>
                  <span class="overview">{{selectedMovie.overview}}</span>
                  
                </div>
                <div class="button-container">
                  <el-button type="success" plain round style="margin-top:15px;">Add Rating</el-button>
                  <el-button type="danger" plain round style="margin-top:15px;" @click="movieTrailer()">Play Trailer</el-button>
                </div>
              </div>
            </el-dialog>

            <el-dialog v-model="videoDialog" title="Movie Trailer">
              <iframe width="560" height="315" :src="videoUrl" frameborder="0" allowfullscreen></iframe>
            </el-dialog>
        </el-main>
      </el-container>
    </div>
  </template>

  <script>
    import axios from 'axios';
    import { ElNotification } from 'element-plus';

    export default {
        data() {
            return {
                actionMovies: [],
                adventureMovies: [],
                animeMovies: [],
                comedyMovies: [],
                crimeMovies: [],
                genres: [],
                recommendedMovies: [],
                showDialog: false,
                videoDialog: false,
                selectedMovie: null,
                videoUrl: "",
                isVideo: false,
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
            this.getRecommended();
        },
        methods: {
            async topActionMovie() {
                await axios.get(`https://api.themoviedb.org/3/discover/movie?api_key=${this.api_key}&with_genres=28&sort_by=popularity.desc`)
                        .then(response => {
                            this.actionMovies = response.data.results.slice(0, 10);
                        });
            },
            async topAdventureMovie() {
              await axios.get(`https://api.themoviedb.org/3/discover/movie?api_key=${this.api_key}&with_genres=12&sort_by=popularity.desc`)
                        .then(response => {
                            this.adventureMovies = response.data.results.slice(0, 10);
                        });
            },
            async topAnimeMovie() {
              await axios.get(`https://api.themoviedb.org/3/discover/movie?api_key=${this.api_key}&with_genres=16&sort_by=popularity.desc`)
                        .then(response => {
                          this.animeMovies = response.data.results.slice(0, 10);
                        })
            },
            async topComedyMovie() {
              await axios.get(`https://api.themoviedb.org/3/discover/movie?api_key=${this.api_key}&with_genres=35&sort_by=popularity.desc`)
                        .then(response => {
                          this.comedyMovies = response.data.results.slice(0, 10);
                        })
            },
            async topCrimeMovie() {
              await axios.get(`https://api.themoviedb.org/3/discover/movie?api_key=${this.api_key}&with_genres=80&sort_by=popularity.desc`)
                        .then(response => {
                          this.crimeMovies = response.data.results.slice(0, 10);
                        })
            },
            async getGenre() {
              await axios.get(`https://api.themoviedb.org/3/genre/movie/list?api_key=${this.api_key}`)
                        .then(response => {
                          const allGenres = response.data;
                          this.genres = allGenres.genres.slice(0, 5);
                        });
            },
            async getRecommended() {
              await axios.get(`https://api.themoviedb.org/3/discover/movie?api_key=${this.api_key}&with_genres=${this.selectedGenreId}&sort_by=vote_average`)
                        .then(response => {
                          this.recommendedMovies = response.data.results.slice(0, 18);
                          console.log("recommended",this.recommendedMovies)
                        })
            },
            openYoutube() {
              this.videoUrl 
            },
            movieTrailer() {
              if (!this.selectedMovie.videos || this.selectedMovie.videos.results.length === 0) {
              ElNotification({
                title: "Error",
                message: "No Trailer found in this movie.",
                type: "error",
                duration: 1000,
              });
            }

              const videoKey = this.selectedMovie.videos.results[0].key;
              
              this.videoUrl = `https://www.youtube.com/embed/${videoKey}`;
              this.videoDialog = true;
            },
            async movieDetails(movie) {

              await axios.get(`https://api.themoviedb.org/3/movie/${movie.id}?api_key=${this.api_key}&append_to_response=videos`)
                .then(response => {
                  console.log(response);
                  this.selectedMovie = response.data;
                  this.showDialog = true;
                });
                console.log("movie", this.selectedMovie);
            },
            async addRating(movie) {
              await axios.post(`https://api.themoviedb.org/3/movie/${movie.id}?api_key=${this.api_key}`)
                .then(response => {
                  console.log('post', response.data);
                });
            }
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
    align-content: center;
    flex-wrap: wrap;
  }
  .el-carousel__item {
    width: 550px;
    height: 380px;
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
    width: 520px;
    height: 350px;
    display: block;
    margin: 15px;
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
  h3 {
    color: white;
  }
  .recommended {
    margin-left:80px;
  }
  .image-container {
    display: flex;
    align-items: flex-start;
  }
  
  .image-wrapper {
    margin-right: 1rem;
  }
  
  .image-info {
    width: 250px;
    height: 250px;
    margin-right: 0px;
  }
  .info-container {
    display: flex;
    align-items: flex-start;
    flex-direction: column;
  }
  .overview {
    font-weight: normal;
    text-align: justify;
    flex: 1;
  }
  iframe {
    display: block;
    margin-left: auto;
    margin-right: auto;
  }
  .button-container {
    display: flex;
    flex-direction: column;
    margin-top: 15px;
    align-items: flex-end;
  }

  .button-container el-button {
    margin-bottom: 5px;
    
  }

  button {
    width: 90px;
  }
  </style>
  