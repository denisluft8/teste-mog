<script setup>
import text from "../assets/GRADE_DESTAQUE_688.txt";
import { ref } from "vue";
import axios from "axios";
import MovieComponent from "../components/MovieComponent.vue";
import RotatingCubeComponent from "../components/RotatingCubeComponent.vue";

const movieTypes = ref([]);
const movies = ref([]);

const fetchFileData = async () => {
  try {
    const response = await axios.get(text);
    const fileData = response.data;

    const movieLines = fileData.split("\n");

    const movieData = {};

    for (const line of movieLines) {
      const nome = line.substring(15, 70).trim();

      if (!movieData[nome]) {
        movieData[nome] = [];
      }

      const movie = {
        codigo: line.substring(2, 10).trim(),
        horario:
          line.substring(10, 12).trim() + ":" + line.substring(13, 15).trim(),
        idade: line.substring(75, 77).trim(),
        legenda: line.substring(80, 81).trim(), //L=legendado, D =dublado, V=versão original, S=sem dialogos
        sessao: line.substring(81, 82).trim(), //S=SESSÃO ESGOTADA/INDISPONÍVEL, N=SESSÃO DISPONÍVEL
        tipoExibicao: line.substring(91, 93).trim(), //NO=(NORMAL), 3D=(3D), XD=(XD), X3=(XD 3D)
        poltrona: line.substring(174, 176).trim(), // "SIGLAS: RE = REGULAR, DB = DB0X, VP = PREMIER
      };

      movieData[nome].push(movie);
    }

    movies.value = movieData;

    movieTypes.value = [
      ...new Set(
        Object.values(movieData).flatMap((movieList) =>
          movieList.map((movie) => movie.tipoExibicao)
        )
      ),
    ];
  } catch (error) {
    console.error("Erro ao acessar o arquivo txt:", error);
  }
};

const handleMovieType = (movieType) => {
  if (movieType === "3D") {
    return "3D";
  } else if (movieType === "NO") {
    return "2D";
  } else if (movieType === "XD") {
    return "XD";
  } else if (movieType === "X3") {
    return movieType;
  }
};

const getMovieAge = (nomeFilme) => {
  const movieList = movies.value[nomeFilme];
  if (movieList && movieList.length > 0) {
    return movieList[0].idade;
  }
  return "";
};

const getMoviesByType = (nomeFilme, movieType) => {
  const movieList = movies.value[nomeFilme];
  if (movieList && movieList.length > 0) {
    const filteredMovies = movieList.filter(
      (movie) => movie.tipoExibicao === movieType
    );
    return filteredMovies.sort((a, b) => {
      const timeA = new Date(`1970/01/01 ${a.horario}`);
      const timeB = new Date(`1970/01/01 ${b.horario}`);
      return timeA - timeB;
    });
  }
  return [];
};

const getMovieTypesByMovie = (nomeFilme) => {
  const movieList = movies.value[nomeFilme];
  if (movieList && movieList.length > 0) {
    return [...new Set(movieList.map((movie) => movie.tipoExibicao))];
  }
  return [];
};

fetchFileData();
</script>

<template>
  <div>
    <template v-for="nomeFilme in Object.keys(movies)">
      <template v-for="movieType in getMovieTypesByMovie(nomeFilme)">
        <div class="movie-row">
          <div class="name-age-container">
            <h2 class="movie-name">{{ nomeFilme }}</h2>
          </div>
          <div class="age-container">
            <p
              :class="{
                'age-orange': getMovieAge(nomeFilme) === '14',
                'age-yellow': getMovieAge(nomeFilme) === '12',
                'age-green': getMovieAge(nomeFilme) === '0',
                'age-blue': getMovieAge(nomeFilme) === '10',
              }"
            >
              {{
                getMovieAge(nomeFilme) === "0" ? "L" : getMovieAge(nomeFilme)
              }}
            </p>
          </div>
          <div v-if="movieType === 'X3'" class="type-container">
            <RotatingCubeComponent :movieType="movieType" />
          </div>
          <div v-else class="type-container">
            <p
              :class="{
                'movie-type': handleMovieType(movieType) !== 'XD',
                'movie-type movie-type-xd': handleMovieType(movieType) === 'XD',
              }"
            >
              {{ handleMovieType(movieType) }}
            </p>
          </div>
          <div class="info-row">
            <template v-for="movie in getMoviesByType(nomeFilme, movieType)">
              <MovieComponent :movie="movie" />
            </template>
          </div>
        </div>
      </template>
    </template>
  </div>
</template>

<style scoped>
.movie-row {
  display: flex;
  max-width: 100%;
  background: #a0a1a4;
}
.movie-row-vip {
  display: flex;
  max-width: 100%;
}
.name-age-container {
  display: flex;
  align-items: center;
  border: 1px solid grey;
  background-color: #222326;
}

.type-container {
  display: flex;
  align-items: center;
  justify-content: center;
  border: 1px solid grey;
  background-image: linear-gradient(to bottom right, #4d4a47, #676363);
  min-width: 72px;
}

.age-container {
  display: flex;
  align-items: center;
  justify-content: center;
  border: 1px solid grey;
  min-width: 60px;
  background-color: #302e34;
}
.movie-name {
  font-size: 20px;
  margin: 0;
  min-width: 320px;
  text-align: left;
  padding-left: 12px;
}

.age-orange,
.age-yellow,
.age-green,
.age-blue {
  font-size: 20px;
  margin: 0;
  width: 30px;
  border-radius: 2px;
  font-weight: 900;
}

.age-orange {
  background-color: #ff7300;
  box-shadow: 1px 1px 5px 1px #000;
}
.age-yellow {
  background-color: #f1ae02;
  box-shadow: 1px 1px 5px 1px #000;
}
.age-green {
  background-color: #02a929;
  box-shadow: 1px 1px 5px 1px #000;
}
.age-blue {
  background-color: #0280e3;
  box-shadow: 1px 1px 5px 1px #000;
}

.movie-type {
  font-size: 32px;
  margin: 0;
  color: #686768;
  font-weight: 900;
  text-shadow: 1px 1px 5px #222;
}

.movie-type-xd {
  font-weight: 600;
  font-size: 32px;
  color: #e6e4e5;
  text-shadow: 1px 1px 10px #aaa;
}

.info-row {
  display: flex;
  flex-wrap: wrap;
}
</style>
