<template>
  <div class="quran-book">
    <div class="quran-pages" ref="quranPages">
      <div v-for="(page, index) in pages" :key="index" class="quran-page">
        <div class="quran-page-header">
          <h2 @click="goToSurah(page.id)">{{ page.name_simple }}</h2>
          <p>{{ page.translated_name.name }}</p>
        </div>
        <div class="quran-page-body">
          <div v-for="(verse, index) in page.verses" :key="index" class="quran-verse" @click="showOptions(verse)">
            <p class="quran-verse-number">{{ verse.verse_number }}</p>
            <div class="quran-verse-text">
              <p v-html="verse.text"></p>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="quran-options" v-if="selectedVerse">
      <button class="btn btn-primary" @click="showTranslation(selectedVerse)">Terjemahan</button>
      <button class="btn btn-primary" @click="showTafsir(selectedVerse)">Tafsir</button>
      <button class="btn btn-primary" @click="playAudio(selectedVerse)">Audio</button>
      <button class="btn btn-primary" @click="bookmark(selectedVerse)">Simpan</button>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      pages: [],
      selectedVerse: null,
      translation: "id",
      tafsir: 131,
      audio: null,
      bookmarks: [],
    };
  },
  mounted() {
    fetch("https://api.quran.com/api/v4/chapters?language=id")
      .then((response) => response.json())
      .then((data) => {
        this.pages = data.chapters;
      });
  },
  methods: {
    showOptions(verse) {
      this.selectedVerse = verse;
    },
    showTranslation(verse) {
      fetch(`https://api.quran.com/api/v4/quran/translations/${this.translation}/verses/${verse.verse_number}`)
        .then((response) => response.json())
        .then((data) => {
          alert(data.verses[0].text);
        });
    },
    showTafsir(verse) {
      fetch(`https://api.quran.com/api/v4/quran/tafsirs/${this.tafsir}/verses/${verse.verse_number}`)
        .then((response) => response.json())
        .then((data) => {
          alert(data.verses[0].tafsirs[0].text);
        });
    },
    playAudio(verse) {
      if (this.audio) {
        this.audio.pause();
      }
      this.audio = new Audio(`https://audio.qurancdn.com/${verse.chapter_id}/${verse.verse_number}/high`);
      this.audio.play();
    },
    bookmark(verse) {
      this.bookmarks.push(verse);
    },
    goToSurah(surahId) {
      // redirect to surah page
      window.location.href = `https://quran.com/${surahId}`;
    }
  },
};
</script>