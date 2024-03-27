<template>
  <v-layout class="rounded rounded-md">
    <v-navigation-drawer>
      <img
        src="../assets/images/mapLogo.png"
        style="max-width: 180px"
        class="mt-4 ml-4"
      />
      <v-list>
        <v-list-item
          v-for="(item, index) in menuItems"
          :key="index"
          :style="
            useRoute().path == item.route
              ? 'background: #087cfc; color: white'
              : ''
          "
          @click="navigateTo(item.route)"
        >
          <v-icon class="mr-2">{{ item.icon }}</v-icon
          >{{ item.title }}</v-list-item
        >
      </v-list>
    </v-navigation-drawer>

    <v-app-bar title="Nuxt 3 Mapbox"></v-app-bar>

    <v-main class="ma-5">
      <slot />
    </v-main>
  </v-layout>
</template>

<script>
import { useRoute } from "vue-router";
import DrawPolygon from "~/components/DrawPolygon.vue";
export default {
  components: { DrawPolygon },
  data() {
    return {
      menuItems: [
        {
          title: "Draw polygon",
          icon: "mdi-draw",
          route: "/"
        },
        {
          title: "Animate routes",
          icon: "mdi-routes",
          route: "/animateRoutes"
        }
      ]
    };
  }
};
</script>
