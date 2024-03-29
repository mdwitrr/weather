<template>
  <div class="user-location">
    <div
      class="user-location__info"
      :class="{ 'user-location__info_visible_false': visibilitySearchForm }"
    >
      <h1 class="user-location__city">{{ cityName }}</h1>
      <SwitchTemperatureUnit class="user-location__switch-temp" />
    </div>
    <div
      class="user-location__buttons"
      :class="{ 'user-location__buttons_visible_false': visibilitySearchForm }"
    >
      <button
        @click="switchSearchForm(!visibilitySearchForm)"
        class="user-location__search-city"
      >
        Сменить город
      </button>
      <button
        @click="updateUserLocation"
        class="user-location__search-location"
      >
        <SvgIcon name="arrow" class="user-location__arrow-icon" />
        Мое местоположение
      </button>
    </div>
    <div
      @click.self="closeSearch"
      class="user-location__search-box"
      :class="{
        'user-location__search-box_visible_true': visibilitySearchForm,
      }"
    >
      <div class="user-location__container">
        <SearchCity class="user-location__search" />
      </div>
    </div>
  </div>
</template>

<script>
import SvgIcon from "./icons/SvgIcon";
import SwitchTemperatureUnit from "./SwitchTemperatureUnit";
import SearchCity from "./SearchCity";
import { mapActions, mapGetters, mapMutations } from "vuex";
export default {
  name: "UserLocation",
  components: { SearchCity, SwitchTemperatureUnit, SvgIcon },
  props: {
    cityName: {
      type: String,
      default: null,
    },
  },
  data() {
    return {
      searchVisible: false,
    };
  },
  computed: mapGetters([
    "locationData",
    "visibilitySearchForm",
  ]),
  methods: {
    ...mapActions(["getLocation", "getWeather"]),
    ...mapMutations(["switchSearchForm"]),
    async updateUserLocation() {
      await this.getLocation();
      await this.getWeather(this.locationData.city);
    },
    closeSearch() {
      this.switchSearchForm();
    },
  },
  mounted() {
    this.switchSearchForm();
  },
};
</script>

<style lang="scss">
.user-location {
  display: grid;
  grid-row-gap: rem(9);
  @media (max-width: 640px) {
    grid-row-gap: rem(16);
  }
  &__city {
    font-size: font-size(3);
    font-weight: weight(1);
    @include base-animate(opacity);
    @media (max-width: 640px) {
      font-size: font-size(2, true);
    }
  }
  &__arrow-icon {
    $size: 20;
    width: rem($size);
    height: rem($size);
    @include base-animate(fill);
    fill: color("light", 0.6);
    transform: rotate(11deg);
  }
  &__info {
    display: grid;
    grid-template-columns: 1fr min-content;
    align-items: flex-start;
    &_visible_false {
      .user-location__city {
        opacity: 0;
      }
      @media (max-width: 640px) {
        .user-location__switch-temp {
          opacity: 0;
        }
      }
    }
  }
  &__search-city,
  &__search-location {
    font-size: font-size(0);
    @include base-animate();
    @media (max-width: 640px) {
      font-size: font-size(-1);
    }
    color: color("light", 0.6);
    &:hover {
      color: color();
      & .user-location__arrow-icon {
        fill: color("light");
      }
    }
  }
  &__search-location {
    display: grid;
    grid-template-columns: min-content 1fr;
    grid-column-gap: rem(11);
    @media (max-width: 640px) {
      grid-column-gap: rem(6);
    }
  }
  &__search {
    position: absolute;
    top: 0;
    left: 0;
    @include base-animate(opacity);
    opacity: 0;
    visibility: hidden;
    margin: rem(80) 0 0 rem(21);

    @media (max-width: 640px) {
      margin: rem(27) 0 0 rem(21);
    }
  }
  &__search-box {
    height: 100vh;
    width: 100vw;
    position: absolute;
    top: 0;
    left: 0;
    visibility: hidden;

    &_visible_true {
      visibility: visible;
      .user-location__search {
        visibility: visible;
        opacity: 1;
      }
    }
  }
  &__container {
    margin: 0 auto;
    max-width: rem(1215);
    position: relative;
  }
  &__buttons {
    display: grid;
    grid-template-columns: repeat(2, max-content);
    margin-right: rem(14);
    @media (min-width: 400px) {
      grid-column-gap: rem(25);
    }
    @include base-animate(opacity);
    &_visible_false {
      opacity: 0;
    }
  }
  &__switch-temp {
    margin-top: rem(6);
    @include base-animate(opacity);
    @media (max-width: 640px) {
      margin-right: rem(14);
    }
  }
}
.forecast_condition_iphone {
  .user-location {
    &__arrow-icon {
      display: none;
    }
    &__buttons {
      width: 100%;
      display: flex;
      flex-wrap: wrap-reverse;
    }
    &__search-location {
      grid-template-columns: 1fr;
    }
  }
}
</style>
