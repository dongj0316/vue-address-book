<template>
  <div class="page-example3">
    <search-bar
      ref="searchBar"
      back
      fixed
      :z-index="6"
      @focus="onFocus"
      @blur="isFocus = false"
      @cancel="searchList = []"
      @clear="searchList = []"
      @search="onSearch"
      @back="$router.back()"
    />
    <v-ibar ref="vibar" :data="mockData" @cell-click="onCellClick" :fixedTop="54">
      <template v-slot="slotProps">
        <div class="cell">
          {{ slotProps.item.name }}
        </div>
      </template>
    </v-ibar>

    <transition name="opacity-fade">
      <div v-show="isFocus" @click="$refs.searchBar.blur()" @touchmove.prevent class="search-overlay"></div>
    </transition>

    <div v-show="hasSearchResult" class="search-result">
      <template v-for="(item, index) in searchList">
        <div class="search-result__cell" @click="onCellClick(item)" :class="{ 'border--t': index !== 0 }" :key="item.letter + item.name">
          {{item.name}}
        </div>
      </template>
    </div>
  </div>
</template>

<script>
import mockData from '../mock.js'
import VIbar from '../../v-ibar/main'
import SearchBar from '../components/search-bar'

export default {
  name: 'page-example3',
  components: {
    VIbar,
    SearchBar
  },
  data () {
    return {
      mockData,
      isFocus: false,
      searchList: []
    }
  },
  computed: {
    hasSearchResult () {
      return this.searchList.length > 0
    }
  },
  methods: {
    onFocus () {
      this.isFocus = true
    },
    onSearch (str) {
      const searchList = this.mockData.filter(item => {
        return item.name.toLowerCase().indexOf(str.toLowerCase()) > -1
      })
      this.searchList = searchList
    },
    onCellClick (item) {
      this.$refs.vibar.scrollTo(item)
      this.$refs.searchBar.cancel()
      this.searchList = []
    }
  }
}
</script>

<style lang="less">
.page-example3 {
  padding-top: 54px;
  .search-overlay {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(0, 0, 0, .1);
    z-index: 5;
  }
  .search-result {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    padding-top: 54px;
    background: #fff;
    overflow: hidden;
    overflow-y: auto;
    z-index: 5;
    &__cell {
      position: relative;
      display: flex;
      align-items: center;
      padding: 16px;
    }
  }
  .border--t:before {
    content: '';
    position: absolute;
    top: 0;
    left: 15px;
    right: 15px;
    pointer-events: none;
    border-top: 1px solid #ededed;
    transform: scaleY(.5);
  }
}

@keyframes fade-in {
  from {
    opacity: 0;
  }
}
@keyframes fade-out {
  to {
    opacity: 0;
  }
}
.opacity-fade {
  &-enter-active {
    animation: fade-in .3s both;
  }
  &-leave-active {
    animation: fade-out .3s both;
  }
}
</style>
