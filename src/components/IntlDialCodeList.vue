<template>
  <ul>
    <li
      v-for="(country, index) in allCountries"
      :key="country.iso2"
      :class="{
                highlighted: activeIndex === index,
                active: country === activeCountry,
            }"
      :title="country | titleize"
      @mousemove="activeIndex = index"
      @click="select(country.iso2)"
    >
      <span :class="['flag', country.iso2.toLowerCase()]"></span>
      <span class="country">{{ country.name }}</span>
    </li>
  </ul>
</template>

<script>
import { allCountries } from '../allCountries';
const STORAGE_NAMESPACE = '__countryCode__';

function getCountry() {
    return fetch('https://geolocation-db.com/json/')
        .then((response) => response.json())
        .then((response) => {
            return response.country_code || null;
        });
}

export default {
    model: {
        event: 'change',
    },
    props: {
        allCountries: {
            type: Array,
            default: () => allCountries,
        },
        fetchDefault: {
            type: Boolean,
            default: true,
        },
    },

    computed: {
        activeCountry() {
            return this.allCountries.find(
                (country) => country.iso2 === this.activeCountryCode,
            );
        },
    },

    data() {
        return {
            activeIndex: null,
            activeCountryCode: null,
        };
    },

    created() {
        const cachedCountryCode = localStorage.getItem(STORAGE_NAMESPACE);

        if (cachedCountryCode) return this.select(cachedCountryCode);
        if (!this.fetchDefault) return;

        getCountry()
            .then((code) => {
                // If user has started browsing, don't update.
                if (this.activeIndex) return;

                const defaultCountryCode =
                    code ||
                    document.documentElement.lang
                        .split('-')
                        .pop()
                        .toUpperCase() ||
                    'US';

                this.select(defaultCountryCode);
            })
            .catch((error) => {
                console.warn(error);
            });
    },

    methods: {
        select(code) {
            this.activeCountryCode = code;
            this.$emit('change', this.activeCountry);
            localStorage.setItem(STORAGE_NAMESPACE, code);
        },
    },

    filters: {
        titleize(country) {
            return `${country.name} +${country.dialCode}`;
        },
    },
};
</script>

<style lang="scss" scoped>
@import '../flags';

ul {
    padding: 1rem 0;
    margin: 0;

    li {
        padding-left: 1rem;
        padding-right: 1rem;
        line-height: 2;
        cursor: pointer;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
        transition: background-color 60ms;

        &.highlighted {
            background-color: lightgray;
        }

        &.active {
            background-color: gray;
        }
    }
}

.flag {
    display: inline-block;
}

.country {
    padding-left: 0.6rem;
    font-weight: 500;
}
</style>
