<template>
  <!-- Navbar -->
  <nav
    style="background-color: #196844"
    class="main-header navbar navbar-expand navbar-white navbar-light"
  >
    <!-- Left navbar links -->
    <ul class="navbar-nav">
      <li class="nav-item">
        <a class="nav-link" data-widget="pushmenu" href="#">
          <i class="fas fa-bars"></i>
        </a>
      </li>
      <header class="header" role="banner">
        <div class="header-inner clearfix">
          <h5>
            <a class="brand pull-left" href="/home">
              <img
                src="/img/logo_header_poli.png"
                alt="Politécnico Colombiano Jaime Isaza Cadavid"
                class="img-responsive logo img-fluid"
              />
            </a>
          </h5>
          <div class="header-search pull-right"></div>
        </div>
      </header>

      <!-- <li class="nav-item d-none d-sm-inline-block">
        <a href="#" class="nav-link">Contact</a>
      </li>-->
    </ul>

    <!-- SEARCH FORM -->
    <!-- <form class="form-inline ml-3">
      <div class="input-group input-group-sm">
        <input class="form-control form-control-navbar" type="search" placeholder="Search" aria-label="Search">
        <div class="input-group-append">
          <button class="btn btn-navbar" type="submit">
            <i class="fas fa-search"></i>
          </button>
        </div>
      </div>
    </form>-->

    <!-- Right navbar links -->
    <ul class="navbar-nav ml-auto">
      <button class="btn btn-light right">
        <a @click="logout" style="cursor: pointer; color: black">Cerrar sesión</a>
      </button>
      <!-- Messages Dropdown Menu -->

      <!-- Notifications Dropdown Menu -->

      <li class="nav-item d-none d-sm-inline-block"></li>
    </ul>
  </nav>

  <!-- /.navbar -->
</template>

<script>
import ApiService from "../services/api.service";
import { TokenService } from "../services/storage.service";
export default {
  props: {
    tipo: Number
  },
  created() {},
  methods: {
    logout() {
      this.$store.commit("setLogin", false);
      this.$store.commit("MUTATION_info_user", []);

      TokenService.saveTokenCustom("visitante", "si");
      this.$store.commit("setVisitante", "si");
      TokenService.removeToken();
      /* let info = JSON.stringify('{"id_rol": 0}')
      console.log(info);
      let encriptado = CryptoJS.AES.encrypt(info, "Key").toString();
      TokenService.saveTokenCustom("user", JSON.stringify(encriptado)); */

      let info = JSON.stringify({"id_rol": 0});

      let encriptado = CryptoJS.AES.encrypt(info, "Key").toString();

      TokenService.saveTokenCustom("user", encriptado);

      this.$store.commit("MUTATION_setRol", 0);
      this.$store.state.rol = 0;
      /* TokenService.saveTokenCustom("user", JSON.stringify(encriptado)); */
      TokenService.removeRefreshToken();
      ApiService.unmount401Interceptor();
      this.$router.push({ name: "home" });
    }
  }
};
</script>

<style>
</style>