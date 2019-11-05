<template>
  <div id="app">
    <v-app>
      <v-app-bar
        fixed
        app
      >
        <v-toolbar-title class="display-1">Тестовое приложение поиска</v-toolbar-title>
        <v-spacer />
        <v-text-field
          v-model="searchString"
          clearable
          label="Поиск по постам"
          prepend-inner-icon="fas fa-search"
          class="mt-7"
        />
      </v-app-bar>
      <v-content>
        <v-container>
          <v-row>
            <v-col
              cols="12"
            >
              <div class="display-1 mb-4">Посты</div>
              <v-card
                v-for="(l, i) in filteredList"
                :key="`key_${i}`"
                outlined
                class="card mb-2"
              >
                <v-card-title v-html="l.title" />
                <v-card-text v-html="l.body" />
              </v-card>

              <div class="body-1 text-center mt-6 font-italic">
                <span v-if="(list.length === 0)">Записей нет...</span>
                <span v-if="(list.length > 0 && filteredList.length === 0)">Нет подходящих постов</span>
              </div>
            </v-col>
          </v-row>
        </v-container>
      </v-content>
    </v-app>
  </div>
</template>

<script>

export default {
  name: 'app',
  components: {
  },
  data() {
    return {
      API_URL: 'https://jsonplaceholder.typicode.com/posts',
      list: [],
      searchString: '',
    };
  },
  mounted() {
    const params = {
      method: 'GET',
      mode: 'cors',
      credentials: 'include',
      cache: 'no-cache',
    };

    fetch(this.API_URL, params)
      .then((response) => {
        if (response.status === 200) {
          return response.json();
        }

        return Promise.reject();
      })
      .then((data) => {
        this.list = data;
      });
  },
  computed: {
    filteredList() {
      const regexTMP = (this.searchString) ? this.searchString.split(' ').filter(el => el.trim()) : [];
      if (regexTMP.length === 0) {
        return this.list;
      }

      const regex = new RegExp(`(${regexTMP.join('|')})`, 'gi');
      return this.list.filter(el => {
        return el.title.match(regex) || el.body.match(regex);
      }).map(el => {
        return {
          ...el,
          title: el.title.replace(regex, '<span class="searched">$&</span>'),
          body: el.body.replace(regex, '<span class="searched text">$&</span>'),
        };
      });
    },
  },
};
</script>


<style lang="stylus">
.card
  white-space pre
.searched
  display inline-block
  position relative
  &::after
    content ''
    position absolute
    display block
    background-color #00AF16
    height 2px
    border-radius 2px
    left 0
    right 0
    bottom 4px
  &.text::after
    height 1px
    bottom 3px
</style>
