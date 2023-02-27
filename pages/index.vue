<template>
  <main class="dark:text-gray-200 text-gray-800 min-h-screen">
    <div class="dark:bg-slate-900 bg-slate-200">
      <!-- div flex row jc ic mxa-auto -->
      <div class="flex flex-row justify-center items-center mx-auto">
        <!-- h1 of Dictionary  and icon  -->
        <div class="mx-4 my-2">
          <h1 class="text-3xl font-bold">
            <span class="text-gray-800 dark:text-gray-300">Dictionary</span>
            <Icon
              name="arcticons:dictionary"
              class="text-gray-900 dark:text-gray-200 h-7 w-7"
            />
          </h1>
        </div>
        <!-- toggle mode -->
        <div class="ml-4">
          <ToggleMode />
        </div>
      </div>
      <div class="mx-4 my-2">
        <input
          type="search"
          role="search"
          class="border dark:border-gray-400 dark:bg-slate-700 bg-slate-500 text-white border-gray-700 rounded w-full py-2 px-3"
          v-model="searchTerm"
          placeholder="Enter a word to search"
          @keydown.enter="search"
        />
      </div>
      <!-- tooltip to tell the user to keydown.enter for a search -->
      <div class="mx-4 my-2">
        <span v-if="searchTerm" class="dark:text-gray-500 text-gray-900"
          >Press Enter to search</span
        >
      </div>

      <!-- loading spinner using tailwindcss -->
      <div v-if="isLoading" class="mx-4 my-2">
        <svg
          class="animate-spin -ml-1 mr-3 h-5 w-5 text-blue-500"
          xmlns="http://www.w3.org/2000/svg"
          fill="none"
          viewBox="0 0 24 24"
        >
          <circle
            class="opacity-25"
            cx="12"
            cy="12"
            r="10"
            stroke="currentColor"
            stroke-width="4"
          ></circle>
          <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8v8H4z"></path>
        </svg>
        <span class="dark:text-gray-200 text-gray-800">Loading...</span>
      </div>
      <!--show warning alert if isError -->
      <div v-if="isError" class="mx-4 my-2">
        <div
          class="bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded relative"
          role="alert"
        >
          <strong class="font-bold"> <Icon name="ic:outline-warning" /> Error!</strong>
          <span class="block sm:inline"> Something went wrong.</span>
          <span class="block sm:inline">
            The word you're searching is not there! or empty!</span
          >
          <span class="absolute top-0 bottom-0 right-0 px-4 py-3">
            <svg
              class="fill-current h-6 w-6 text-red-500"
              role="button"
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 20 20"
              @click="isError = false"
            >
              <title>Close</title>
              <path
                d="M14.348 14.849a1.2 1.2 0 0 1-1.697 0L10 11.819l-2.651 3.029a1.2 1.2 0 1 1-1.697-1.697l2.758-3.15-2.759-3.152a1.2 1.2 0 1 1 1.697-1.697L10 8.183l2.651-3.031a1.2 1.2 0 1 1 1.697 1.697l-2.758 3.152 2.758 3.15a1.2 1.2 0 0 1 0 1.698z"
              />
            </svg>
          </span>
        </div>
      </div>
      <div v-if="wordData" class="mx-4 my-2">
        <div v-for="(word, index) in wordData" :key="index">
          <h1 class="text-2xl font-bold dark:text-gray-200 text-gray-800">
            {{ word.word }}
          </h1>
          <p class="dark:text-gray-500 text-gray-900 italic">{{ word.origin }}</p>
          <div>
            <p class="text-lg font-medium dark:text-gray-200 text-gray-800">Phonetics:</p>
            <!-- phonetic text -->

            <div class="flex items-center">
              <span class="mr-2 dark:text-gray-200 text-gray-800">{{
                word.phonetics[0].text
              }}</span>
              <audio controls class="outline-none">
                <source :src="word.phonetics[0].audio" type="audio/mpeg" />
                Your browser does not support the audio element.
              </audio>
            </div>
          </div>
          <div>
            <p class="text-lg font-medium mt-4 dark:text-gray-200 text-gray-800">
              Meanings:
            </p>
            <ul class="list-disc pl-4">
              <li v-for="(meaning, index) in word.meanings" :key="index">
                <p class="font-medium dark:text-gray-200 text-gray-800">
                  {{ meaning.partOfSpeech }}
                </p>
                <ul class="list-disc pl-4">
                  <li v-for="(definition, index) in meaning.definitions" :key="index">
                    <p class="dark:text-blue-600 text-gray-800">
                      {{ definition.definition }}
                    </p>
                    <p
                      v-if="definition.example"
                      class="italic dark:text-gray-500 text-gray-900"
                    >
                      Example:
                      <span class="dark:text-gray-300 text-gray-800">{{
                        definition.example
                      }}</span>
                    </p>
                    <div v-if="definition.synonyms.length > 0">
                      <p class="font-medium dark:text-gray-200 text-gray-800">
                        Synonyms:
                      </p>
                      <ul class="list-disc pl-4">
                        <li
                          v-for="(synonym, index) in definition.synonyms"
                          :key="index"
                          class="dark:text-gray-200 text-gray-800"
                        >
                          {{ synonym }}
                        </li>
                      </ul>
                    </div>
                    <div v-if="definition.antonyms.length > 0">
                      <p class="font-medium dark:text-gray-200 text-gray-800">
                        Antonyms:
                      </p>
                      <ul class="list-disc pl-4">
                        <li
                          v-for="(antonym, index) in definition.antonyms"
                          :key="index"
                          class="dark:text-gray-200 text-gray-800"
                        >
                          {{ antonym }}
                        </li>
                      </ul>
                    </div>
                  </li>
                </ul>
              </li>
            </ul>
          </div>
        </div>
      </div>
      <div v-else class="mx-4 my-2 dark:text-gray-200 text-gray-800">
        <p>Search for a word to get started.</p>
      </div>
      <div class="mx-4 my-2">
        <p class="dark:text-gray-500 text-gray-900">
          Powered by
          <a href="https://dictionaryapi.dev/" target="_blank" rel="noopener noreferrer"
            >Dictionary API</a
          >
        </p>
      </div>

      <Foot />
    </div>
  </main>
</template>

<script>
export default {
  components: {},
  data() {
    return {
      searchTerm: "",
      wordData: null,
      isLoading: false,
      isError: false,
    };
  },
  methods: {
    async search() {
      this.isLoading = true;
      this.isError = false;

      try {
        if (this.searchTerm.length > 0) {
          const response = await fetch(
            `https://api.dictionaryapi.dev/api/v2/entries/en/${this.searchTerm}`
          );
          if (response.ok) {
            this.wordData = await response.json();
          } else {
            this.wordData = null;
            this.isError = true;
          }
        } else {
          this.wordData = null;
          this.isError = true;
        }
      } catch (error) {
        console.error(error);
        this.isError = true;
      }

      this.isLoading = false;
    },
  },
};
</script>
