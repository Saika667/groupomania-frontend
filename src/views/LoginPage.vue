<script>
  import LoginForm from "../components/pairedComponent/LoginForm.vue";
  import Menu from "../components/menuComponent/Menu.vue";
  import MenuItem from "../components/menuComponent/MenuItem.vue";
  import Logo from "../components/atomicComponents/Logo.vue";
  import Footer from "../components/baseComponents/Footer.vue";
  // Création du composant principal App
  export default {
    name: "LoginPage",
    components: {
      LoginForm,
      Menu,
      MenuItem,
      Logo,
      Footer,
    },
  }
</script>

<template>
  <div class="background">
    <Logo imageAddress="../images/icon-left-font-monochrome-white.svg"/>
    <Menu>
        <MenuItem href="/login" v-bind:isActive="true">Se connecter</MenuItem>
        <MenuItem href="/register">S'inscrire</MenuItem>
    </Menu>
    <LoginForm></LoginForm>
    <Footer />
  </div>
</template>

<style lang="scss">
    
</style>

<style lang="scss" scoped>
   .background {
        width: 100%;
        height: 100%;
        min-height: 100vh;
        background: url('../images/background.png') center;
        background-size: cover;
        position: relative;
        padding-bottom: 80px;
        box-sizing: border-box;
    }

</style>
