<template>
  <div>
    <b-container fluid class="px-0 px-md-5 mt-5">
      <hr />
    </b-container>
    <b-container class="footer my-3">
      <b-row class="justify-content-md-center text-center">
        <b-col cols="12" md="2">
          <router-link to="/about" class="text-secondary"> About </router-link>
        </b-col>
        <b-col cols="12" md="2">
          <router-link to="/help" class="text-secondary"> Help </router-link>
        </b-col>
        <b-col cols="12" md="2">
          <router-link to="/contact" class="text-secondary">
            Contact us
          </router-link>
        </b-col>
        <b-col cols="12" md="2">
          <router-link to="/report" class="text-secondary">
            Report issue
          </router-link>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<style lang="sass" scoped>
@import src/style/bootstrap-custom.scss
@import bootstrap/scss/bootstrap

.footer
  text-align: center
  height: fill
  color: $secondary
  font-size: 10pt
</style>
