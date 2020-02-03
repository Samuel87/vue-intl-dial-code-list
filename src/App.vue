<template>
  <div id="app">
    {{ country }}
    <intl-phone-list v-model="country"></intl-phone-list>
  </div>
</template>

<script>
import IntlPhoneList from './components/IntlDialCodeList.vue';

export default {
    components: {
        IntlPhoneList,
    },
    data() {
        return {
            country: null,
        };
    },
};
</script>

<style>
html {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto,
        'Helvetica Neue', Arial, 'Noto Sans', sans-serif, 'Apple Color Emoji',
        'Segoe UI Emoji', 'Segoe UI Symbol', 'Noto Color Emoji';
    line-height: 1.5;
}
</style>
