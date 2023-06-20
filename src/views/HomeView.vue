<script setup>
import text from "../assets/GRADE_DESTAQUE_688.txt";
import { ref } from "vue";
import axios from "axios";
import MovieComponent from "../components/MovieComponent.vue";

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
        sala: line.substring(0, 2).trim(),
        codigo: line.substring(2, 10).trim(),
        horario: line.substring(10, 15).trim(),
        idade: line.substring(75, 77).trim(),
        vazio: line.substring(78, 79).trim(),
        legenda: line.substring(80, 81).trim(), //L=legendado, D =dublado, V=versão original, S=sem dialogos
        sessao: line.substring(81, 82).trim(), //S=SESSÃO ESGOTADA/INDISPONÍVEL, N=SESSÃO DISPONÍVEL
        estreia: line.substring(82, 83).trim(), //N=CÓPIA NORMAL, P=PRÉ-ESTREIA
        tresD: line.substring(83, 84).trim(), //S=FILME 3D, N=FILME 2D, X=FILME XD 2D, Y=FILME XD 3D
        vip: line.substring(84, 85).trim(), //S=SIM (100% VIP), H=HIBRIDO (VIP e REGULAR), N=NÃO (SEM LUGARES VIP)
        duracao: line.substring(85, 88).trim(), //duração em minutos
        lotacao: line.substring(89, 90).trim(), //M=MUITOS (DE 0% A 59%), P=POUCOS (DE 60% A 89% ), L=LOTADO (DE 90% A 100%)
        tipoExibicao: line.substring(91, 93).trim(), //NO=(NORMAL), 3D=(3D), XD=(XD), X3=(XD 3D)
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
    return movieList.filter((movie) => movie.tipoExibicao === movieType);
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
          <div class="name-age-container">
            <p class="movie-age">
              {{
                getMovieAge(nomeFilme) === "0" ? "L" : getMovieAge(nomeFilme)
              }}
            </p>
          </div>
          <div class="name-age-container">
            <p class="movie-age">{{ movieType }}</p>
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
}
.name-age-container {
  display: flex;
  align-items: center;
  border: 1px solid grey;
}
.movie-name {
  font-size: 20px;
  margin: 0;
  min-width: 300px;
  text-align: left;
  padding-left: 12px;
}

.movie-age {
  font-size: 20px;
  margin: 0;
  min-width: 60px;
}

.info-row {
  display: flex;
  flex-wrap: wrap;
}
</style>
