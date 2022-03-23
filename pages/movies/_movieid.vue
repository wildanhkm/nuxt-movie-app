<template>
  <Loading v-if="$fetchState.pending" />
  <div v-else class="container single-movie">
    <NuxtLink :to="{ name: 'index' }" class="button">← Back</NuxtLink>
    <div class="movie-info">
      <div class="movie-img">
        <img
          :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`"
          :alt="`${movie.original_title}`"
        />
      </div>
      <div class="movie-content">
        <h1>Title: {{ movie.title }}</h1>
        <p class="movie-fact tagline">
          <span>Tagline:&nbsp;</span>"{{ movie.tagline }}"
        </p>
        <p class="movie-fact">
          <span>Released:&nbsp;</span>
          {{
            new Date(movie.release_date).toLocaleString('en-us', {
              month: 'long',
              day: 'numeric',
              year: 'numeric',
            })
          }}
        </p>
        <p class="movie-fact">
          <span>Duration:&nbsp;</span>{{ movie.runtime }} minutes
        </p>
        <p class="movie-fact">
          <span>Genre:&nbsp;</span>
          <span
            v-for="(genre, index) in movie.genres"
            :key="index"
            class="no-underline"
          >
            {{ genre.name }},
          </span>
        </p>
        <p class="movie-fact">
          <span>Language:&nbsp;</span>{{ movie.original_language }}
        </p>
        <p class="movie-fact">
          <span>Revenue:&nbsp;</span>
          {{
            movie.revenue.toLocaleString('en-US', {
              style: 'currency',
              currency: 'USD',
            })
          }}
        </p>
        <p class="movie-fact">
          <span>Overview:&nbsp;</span>
          {{ movie.overview }}
        </p>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'SingleMovie',
  data() {
    return {
      movie: {},
    }
  },
  async fetch() {
    await this.getMovie()
  },
  fetchDelay: 500,
  head() {
    return {
      title: this.movie.title,
      meta: [
        {
          hid: 'description',
          name: 'description',
          content: `Movie overview: ${this.movie.overview}`,
        },
        {
          hid: 'keywords',
          name: 'keywords',
          content: this.movie.title,
        },
      ],
    }
  },
  methods: {
    async getMovie() {
      const data = axios.get(
        `https://api.themoviedb.org/3/movie/${this.$route.params.movieid}?api_key=${process.env.apiKey}&language=en-US&page=1`
      )
      const result = await data
      this.movie = result.data
    },
  },
}
</script>

<style lang="scss" scoped>
.single-movie {
  color: #fff;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  padding: 32px 7rem;
  .button {
    align-self: flex-start;
    margin-bottom: 32px;
  }
  .movie-info {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 32px;
    color: #fff;
    @media (min-width: 800px) {
      flex-direction: row;
      align-items: flex-start;
    }
    .movie-img {
      img {
        max-height: 500px;
        width: 100%;
        @media (min-width: 800px) {
          max-height: 700px;
          width: initial;
        }
      }
    }
    .movie-content {
      h1 {
        font-size: 56px;
        font-weight: 400;
      }
      .movie-fact {
        margin-top: 12px;
        font-size: 20px;
        line-height: 1.5;
        span {
          font-weight: 600;
          text-decoration: underline;
          &.no-underline {
            text-decoration: none;
          }
        }
      }
      .tagline {
        font-style: italic;
        span {
          font-style: normal;
        }
      }
    }
  }
}
</style>
