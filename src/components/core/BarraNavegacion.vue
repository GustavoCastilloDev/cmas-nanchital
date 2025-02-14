<template>
  <div>
    <v-app-bar color="white" dark>
      <router-link :to="{ name: 'Home' }">
        <div class="d-flex align-center">
          <v-img
            alt="Vuetify Logo"
            class="shrink mr-2"
            contain
            src="@/assets/logo-menu.png"
            transition="scale-transition"
            max-width="60%"
          />
        </div>
      </router-link>

      <v-spacer></v-spacer>

      <div class="d-none d-lg-flex">
        <v-btn
          active-class="activeClass"
          class="black--text text-capitalize rounded-pill mx-1 btn__hover"
          color="white"
          depressed
          :to="{ name: 'Nosotros' }"
          >Nosotros</v-btn
        >

        <v-menu offset-y open-on-hover>
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              class="text-capitalize rounded-pill mx-1 btn__hover d-none"
              :class="
                $route.meta.pather === 'categoria'
                  ? 'activeClass'
                  : 'black--text'
              "
              color="white"
              depressed
              v-bind="attrs"
              v-on="on"
            >
              Categorías <v-icon right> mdi-chevron-down </v-icon>
            </v-btn>
          </template>
          <v-list>
            <v-list-item
              active-class="activeClass"
              v-for="(item, index) in categorias"
              :key="index"
              link
              :to="{
                name: 'Categoria',
                params: {
                  id: item.id,
                },
              }"
            >
              <v-list-item-title class="text-capitalize">{{
                item.nombre
              }}</v-list-item-title>
            </v-list-item>
          </v-list>
        </v-menu>

        <v-menu offset-y open-on-hover>
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              class="text-capitalize rounded-pill mx-1 btn__hover"
              :class="
                $route.meta.pather === 'pagos' ? 'activeClass' : 'black--text'
              "
              color="white"
              depressed
              v-bind="attrs"
              v-on="on"
            >
              Pagos <v-icon right> mdi-chevron-down </v-icon>
            </v-btn>
          </template>
          <v-list>
            <v-list-item
              active-class="activeClass"
              v-for="(item, index) in pagos"
              :key="index"
              link
              :to="{ name: item.to }"
            >
              <v-list-item-title>{{ item.title }}</v-list-item-title>
            </v-list-item>
          </v-list>
        </v-menu>
        <v-btn
          active-class="activeClass"
          class="black--text text-capitalize rounded-pill mx-1 btn__hover"
          color="white"
          depressed
          :to="{ name: 'Contacto' }"
          >Contacto</v-btn
        >

        <v-menu offset-y open-on-hover>
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              class="text-capitalize rounded-pill mx-1 btn__hover"
              :class="
                $route.meta.pather === 'reportes'
                  ? 'activeClass'
                  : 'black--text'
              "
              color="white"
              depressed
              v-bind="attrs"
              v-on="on"
            >
              Reportes <v-icon right> mdi-chevron-down </v-icon>
            </v-btn>
          </template>
          <v-list>
            <v-list-item
              active-class="activeClass"
              v-for="(item, index) in reportes"
              :key="index"
              link
              :to="{ name: item.to }"
            >
              <v-list-item-title>{{ item.title }}</v-list-item-title>
            </v-list-item>
          </v-list>
        </v-menu>
        <v-btn
          active-class="activeClass"
          class="black--text text-capitalize rounded-pill mx-1 btn__hover"
          color="white"
          depressed
          :to="{ name: 'CuidadoDelAgua' }"
          >Cuidado del agua</v-btn
        >
        <v-btn
          v-if="!session"
          active-class="activeClass"
          class="rounded-pill text-capitalize rounded-pill mx-1 btn__hover"
          color="#01AB55"
          dark
          depressed
          :to="{ name: 'Login' }"
          >Login</v-btn
        >
        <v-btn
          v-if="session && rol === 'admin'"
          active-class="activeClass"
          class="black--text text-capitalize rounded-pill mx-1 btn__hover"
          color="white"
          depressed
          :to="{ name: 'Admin' }"
          >Admin</v-btn
        >
        <v-btn
          v-if="session && rol === 'user'"
          active-class="activeClass"
          class="black--text text-capitalize rounded-pill mx-1 btn__hover"
          color="white"
          depressed
          :to="{ name: 'User' }"
          >User</v-btn
        >
        <v-btn
          v-if="session"
          class="rounded-pill text-capitalize rounded-pill mx-1 btn__hover"
          color="#01AB55"
          dark
          depressed
          :to="{ name: 'Home' }"
          @click="cerrarSession"
          >Cerrar sesión</v-btn
        >

        <v-btn
          @click="$router.push({ name: 'Cart' })"
          active-class="activeClass"
          class="rounded-pill text-capitalize rounded-pill mx-1 btn__hover"
          color="#01AB55"
          dark
          depressed
        >
          <v-badge color="orange" :content="cartCount.toString()">
            <v-icon>mdi-cart</v-icon></v-badge
          >
        </v-btn>
      </div>
      <v-app-bar-nav-icon
        class="d-lg-none"
        @click="drawer = !drawer"
        color="black"
      ></v-app-bar-nav-icon>
    </v-app-bar>
    <navigation-drawer
      :drawer="drawer"
      @close="drawer = $event"
    ></navigation-drawer>
  </div>
</template>

<script>
import { mapState, mapActions } from 'vuex';
import { db } from '../../helpers/Firebase';

export default {
  name: 'BarraNavegacion',
  mounted() {
    this.getCategorias();
  },
  components: {
    NavigationDrawer: () => import('./NavigationDrawer'),
  },
  data: () => ({
    prueba: 2,
    drawer: false,
    pagos: [
      {
        title: 'Pago de servicios',
        to: 'PagoServicios',
      },
      {
        title: 'Historial de pagos',
        to: 'HistorialPagos',
      },
    ],
    reportes: [
      {
        title: 'Generar reporte',
        to: 'GenerarReporte',
      },
      {
        title: 'Estatus reporte',
        to: 'EstatusReporte',
      },
    ],
    categorias: [],
  }),
  methods: {
    ...mapActions(['cerrarSession']),
    async getCategorias() {
      try {
        const { docs } = await db.collection('categorias').get();
        this.categorias = docs.map((e) => {
          const item = {};
          item.nombre = e.data().nombre;
          item.id = e.id;
          return item;
        });
      } catch (error) {
        console.warn(error);
      }
    },
  },
  computed: {
    ...mapState(['session', 'rol', 'cartCount']),
  },
};
</script>

<style lang="scss" scoped>
.activeClass {
  background-color: rgba(46, 186, 115, 0.01) !important;
  color: rgb(46, 186, 115) !important;
}

.btn__hover:hover {
  background-color: rgba(46, 186, 115, 0.7) !important;
  color: #ffff !important;
}

.cerrarSession {
  background-color: #ffff !important;
}
</style>
