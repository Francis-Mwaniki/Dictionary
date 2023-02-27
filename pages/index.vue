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
          type="text"
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
      <div v-if="isError" class="mx-4 my-2">
        <p>Something went wrong. Please try again.</p>
      </div>

      <div v-if="wordData" class="mx-4 my-2">
        <div v-for="(word, index) in wordData" :key="index">
          <h1 class="text-2xl font-bold">{{ word.word }}</h1>
          <p class="dark:text-gray-500 text-gray-900 italic">{{ word.origin }}</p>
          <div>
            <p class="text-lg font-medium dark:text-gray-200 text-gray-800">Phonetics:</p>
            <!-- phonetic text -->

            <div class="flex items-center">
              <span class="mr-2 dark:text-gray-600 text-gray-800">{{
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
                <p class="font-medium">{{ meaning.partOfSpeech }}</p>
                <ul class="list-disc pl-4">
                  <li v-for="(definition, index) in meaning.definitions" :key="index">
                    <p class="dark:text-gray-600 text-gray-800">
                      {{ definition.definition }}
                    </p>
                    <p
                      v-if="definition.example"
                      class="italic dark:text-gray-500 text-gray-900"
                    >
                      Example: {{ definition.example }}
                    </p>
                    <div v-if="definition.synonyms.length > 0">
                      <p class="font-medium dark:text-gray-200 text-gray-800">
                        Synonyms:
                      </p>
                      <ul class="list-disc pl-4">
                        <li v-for="(synonym, index) in definition.synonyms" :key="index">
                          {{ synonym }}
                        </li>
                      </ul>
                    </div>
                    <div v-if="definition.antonyms.length > 0">
                      <p class="font-medium dark:text-gray-200 text-gray-800">
                        Antonyms:
                      </p>
                      <ul class="list-disc pl-4">
                        <li v-for="(antonym, index) in definition.antonyms" :key="index">
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
        const response = await fetch(
          `https://api.dictionaryapi.dev/api/v2/entries/en_US/${this.searchTerm}`
        );
        const data = await response.json();

        this.wordData = data;
        console.log(data);
      } catch (error) {
        console.error(error);
        this.isError = true;
      }

      this.isLoading = false;
    },
  },
};
</script>
