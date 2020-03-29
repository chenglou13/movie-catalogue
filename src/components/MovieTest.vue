<template>
<div class="main">
  <div class="lhs">
    <div class="search-box">
      <input v-model="searchQuery" v-on:keyup="search()" placeholder="Search Movies">
    </div>
    <div class="search-list">
      <p v-if="!searchResults && !searching" class="searchMsg">{{searchMsg}}</p>
      <div class="loader" v-if="searching">
        <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" style="margin: auto; background: none; display: block; shape-rendering: auto;" width="30px" height="30px" viewBox="0 0 100 100" preserveAspectRatio="xMidYMid">
          <circle cx="50" cy="50" fill="none" stroke="#3097D1" stroke-width="10" r="35" stroke-dasharray="164.93361431346415 56.97787143782138" transform="rotate(212.888 50 50)">
            <animateTransform attributeName="transform" type="rotate" repeatCount="indefinite" dur="1s" values="0 50 50;360 50 50" keyTimes="0;1"></animateTransform>
          </circle>
        </svg>
      </div>
      <div class="search-results-box" v-for="movie in searchResults" :key="movie.imdbID" @click="clickMovie(movie.imdbID)">
        <div class="title">
          {{movie.Title}}
        </div>
        <div class="year">
          {{movie.Year}}
        </div>
      </div>
    </div>

    <div class="pagination" v-if="searchResults">
      <div class="prev" v-if="currentPage > 1" @click="changePage('prev')">
        Prev
      </div>
      <div class="next" v-if="totalResults > 10 && totalResults > currentPage*10" @click="changePage('next')">
        Next
      </div>
      <div class="page">
        Page {{currentPage}}
      </div>
      <div class="total-results">
        {{totalResults}} results
      </div>
    </div>
  </div>
  <div class="rhs">
    <p v-if="!selectedMovie && !searchingSelected" class="no-selection">No movie selected</p>
    <div class="loader" v-if="searchingSelected">
      <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" style="margin: auto; background: none; display: block; shape-rendering: auto;" width="30px" height="30px" viewBox="0 0 100 100" preserveAspectRatio="xMidYMid">
        <circle cx="50" cy="50" fill="none" stroke="#3097D1" stroke-width="10" r="35" stroke-dasharray="164.93361431346415 56.97787143782138" transform="rotate(212.888 50 50)">
          <animateTransform attributeName="transform" type="rotate" repeatCount="indefinite" dur="1s" values="0 50 50;360 50 50" keyTimes="0;1"></animateTransform>
        </circle>
      </svg>
    </div>
    <div class="container" v-if="selectedMovie">
      <div class="movie-desc">

        <div class="container">

          <div class="movie-main">
            <div class="title">
              {{selectedMovie.Title}}
            </div>

            <div class="genre">
              {{selectedMovie.Genre}}
            </div>

            <div class="plot">
              <span>Movie plot - </span>
              {{selectedMovie.Plot}}
            </div>
          </div>

          <div class="movie-other">
            <div class="language">
              <label>Language:</label>
              {{selectedMovie.Language}}
            </div>

            <div class="director">
              <label>Director:</label>
              {{selectedMovie.Director}}
            </div>

            <div class="actors">
              <label>Actors:</label>
              {{selectedMovie.Actors}}
            </div>

            <div class="runtime">
              <label>Duration:</label>
              {{selectedMovie.Runtime}}
            </div>

          </div>
        </div>

      </div>
      <div class="movie-poster" v-bind:style="{ backgroundImage: 'url(' + selectedMovie.Poster + ')' }">
      </div>
    </div>
  </div>
</div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'MovieTest',
  props: {},
  data() {
    return {
      searchQuery: null,
      searchResults: null,
      totalResults: null,
      selectedMovie: null,
      searching: false,
      searchingSelected: false,
      currentPage: 1,
      searchMsg: 'Start by searching for movie above'
    }
  },
  methods: {
    getMovies() {

      axios.get(`https://www.omdbapi.com/?apikey=48519cfa&s=${this.searchQuery}&page=${this.currentPage}&type=movie`).then((response) => {
        console.log(response);
        if (response.data.Response == "False") {
          this.searchMsg = response.data.Error;
        }
        this.searchResults = response.data.Search;
        this.totalResults = response.data.totalResults;
        this.searching = false;
      }).catch((error) => {
        console.log(error);
      });

    },
    search() {

      if (this.timer) {
        clearTimeout(this.timer);
        this.timer = null;
      }
      this.timer = setTimeout(() => {
        this.searching = true;
        this.currentPage = 1;
        this.getMovies();
      }, 700);

    },
    changePage(state) {

      this.searchResults = null;

      if (state == "next" && !this.searching) {
        this.currentPage++;
        this.searching = true;
      } else if (state == "prev" && !this.searching) {
        this.currentPage--;
        this.searching = true;
      }

      this.getMovies();

    },
    clickMovie(id) {

      this.selectedMovie = null;
      this.searchingSelected = true;

      axios.get(`https://www.omdbapi.com/?apikey=48519cfa&s=&i=${id}&plot=full`).then((response) => {

        console.log(response);
        this.selectedMovie = response.data;
        this.searchingSelected = false;

      }).catch((error) => {
        console.log(error);
      });

    }
  },
  created() {

  }
}
</script>

<style lang="scss" scoped>
.main {
    display: flex;
    max-width: 1200px;
    margin: 0 auto;
    padding-top: 20px;

    .lhs {
        width: 100%;
        max-width: 300px;

        .search-box {
            padding-bottom: 15px;
            border-bottom: 3px solid #172B4D;
            input {
                width: fill-available;
                border: 1px solid #ccc;
                border-radius: 3px;
                padding: 10px;
                font-size: 14px;
            }
        }

        .search-list {
            overflow: auto;
            height: auto;
            max-height: calc(100vh - 200px);
            min-height: 450px;
            padding: 0 5px 15px;

            .searchMsg {
                text-align: center;
                font-size: 14px;
            }
        }

        .search-results-box {
            margin-top: 15px;
            padding: 20px 15px 30px;
            border-radius: 3px;
            box-shadow: 0 5px 5px 0 rgba(0,0,0,0.09);
            background-color: #fff;
            cursor: pointer;
            position: relative;

            &:hover {
                background-color: rgba(#172B4D, 0.2);
            }

            .title {
                font-size: 15px;
                line-height: 20px;
                width: 87%;
                font-weight: 600;
            }

            .year {
                position: absolute;
                bottom: 15px;
                right: 15px;
            }
        }
        .pagination {
            margin-top: 20px;
            background-color: #172B4D;
            color: #fff;
            padding: 10px;
            text-align: center;
            line-height: 26px;
            position: relative;

            .prev {
                position: absolute;
                left: 15px;
                top: 50%;
                transform: translateY(-50%);
                cursor: pointer;

                &:hover {
                    color: #ccc;
                }
            }
            .next {
                position: absolute;
                right: 15px;
                top: 50%;
                transform: translateY(-50%);
                cursor: pointer;

                &:hover {
                    color: #ccc;
                }
            }
        }
    }

    .rhs {
        width: 100%;
        padding-left: 30px;

        .no-selection {
            text-align: center;
            font-size: 14px;
        }

        .movie-desc {
            display: inline-block;
            width: 65%;
            vertical-align: top;

            .container {
                padding-right: 30px;
                margin: 0;
            }

            .movie-main,
            .movie-other {
                margin-top: 0;
                background-color: #fff;
                padding: 18px 24px;
                box-shadow: 0 5px 14px 0 rgba(0,0,0,0.09);
                border-radius: 3px;
            }

            label {
                font-weight: 600;
            }
            div {
                margin: 12px 0;
            }
            .title {
                margin-top: 0;
                font-size: 32px;
                font-weight: 600;
            }
            .genre {
                font-size: 12px;
            }
            .plot {
                margin: 20px 0 10px;
                line-height: 23px;
            }
        }

        .movie-poster {
            display: inline-block;
            background-repeat: no-repeat;
            background-size: cover;
            background-position: center center;
            width: 35%;
            height: 450px;
        }

    }
}
</style>
