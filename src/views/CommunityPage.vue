<script>
    import Header from "../components/baseComponents/Header.vue";
    import Logo from "../components/atomicComponents/Logo.vue";
    import MenuItemHome from "../components/menuComponent/MenuItemHome.vue";
    import MenuHome from "../components/menuComponent/MenuHome.vue";
    import CommunityView from "../components/CommunityView.vue";
    import CommunityCard from "../components/pairedComponent/CommunityCard.vue";
    import ConfirmAction from "../components/atomicComponents/ConfirmAction.vue";
    import Toaster from "../components/atomicComponents/Toaster.vue";
    import jwt_decode from "jwt-decode";

    export default {
        name: "CommunityPage",
        components: {
            Header,
            Logo,
            MenuItemHome,
            MenuHome,
            CommunityView,
            CommunityCard,
            ConfirmAction,
            Toaster
        },
        data: function() {
            return {
                // Identique à process.env côté node
                apiUrl: import.meta.env.VITE_API_URL,
                users: [],
                isAdmin: false
            }
        },
        methods: {
            showConfirmAction: function(refId) {
                this.$refs.confirm.isVisible = true;
                this.$refs.confirm.targetId = refId;
            },
            deleteUser: function(targetId) {
                // appelle la méthode deleteUser du composant CommunityCard
                this.$refs.cards[targetId].deleteUser();
            },
            showToaster: function(type, message) {
                this.$refs.toaster.show(type, message);
            },
            refreshCards: function() {
                const bearer = localStorage.getItem('userToken');
                const self = this;

                fetch(`${this.apiUrl}/users`, {
                    method: "GET",
                    headers: {
                        'Accept': 'application/json',
                        'Content-Type': 'application/json',
                        'Authorization': `Bearer ${bearer}`
                    },
                }).then(function(res) {
                    if (res.ok) {
                        return res.json();
                    }
                    throw new Error("Quelque chose s'est mal passé");
                }).then(function(res) {
                    self.users = res;
                }).catch((exception) => {
                    self.$refs.toaster.show('error', exception.message);
                });
            }
        },
        async created() {
            const bearer = localStorage.getItem('userToken');
            const self = this;
            //décodage du token avec verify
            const decodedToken = jwt_decode(bearer);
            //récupère le userId décodé
            const userId = decodedToken.userId;

            fetch(`${this.apiUrl}/users`, {
                method: "GET",
                headers: {
                    'Accept': 'application/json',
                    'Content-Type': 'application/json',
                    'Authorization': `Bearer ${bearer}`
                },
            }).then(function(res) {
                if (res.ok) {
                    return res.json();
                }
                throw new Error("Quelque chose s'est mal passé");
            }).then(function(res) {
                self.users = res;
            }).catch((exception) => {
                self.$refs.toaster.show('error', exception.message);
            });
            
            fetch(`${this.apiUrl}/users/${userId}`, {
                method: "GET",
                headers: {
                    'Accept': 'application/json',
                    'Content-Type': 'application/json',
                    'Authorization': `Bearer ${bearer}`
                },                
            }).then(function(res) {
                if (res.ok) {
                    return res.json();
                }
                throw new Error("Quelque chose s'est mal passé");
            }).then(function(res) {
                self.isAdmin = res.user.isAdmin;
            }).catch((exception) => {
                self.$refs.toaster.show('error', exception.message);
            });
        }
    }
</script>

<template>
    <Header imageAddress="../images/icon-cropped-white.svg"/>
    <Toaster ref="toaster"></Toaster>
    <ConfirmAction ref="confirm" @delete="deleteUser" @show-confirmation="showConfirmAction"></ConfirmAction>
    <MenuHome>
        <router-link to="/home">
            <MenuItemHome iconClass="fas fa-house-chimney">Accueil</MenuItemHome>
        </router-link>
        <router-link to="/profil">
            <MenuItemHome iconClass="fas fa-user">Mon profil</MenuItemHome>
        </router-link>
        <router-link to="/community">
            <MenuItemHome iconClass="fas fa-users">Communauté</MenuItemHome>
        </router-link>
        <MenuItemHome iconClass="fas fa-bell">Mes notifications</MenuItemHome>
        <MenuItemHome iconClass="fas fa-power-off" v-bind:isLogout="true">Déconnexion</MenuItemHome>
    </MenuHome>
    <div class="community">
        <CommunityView>
            <CommunityCard v-for="(user, index) in users" v-bind:user="user" ref="cards" v-bind:isAdmin="isAdmin"
                           v-bind:key="index" @show-confirmation="showConfirmAction" @toaster-event="showToaster"
                           @refresh-cards="refreshCards"
            ></CommunityCard>
        </CommunityView>
    </div>
</template>

<style scoped lang="scss">
    .community {
        background-color: lighten(#4E5166, 60);
        padding: 80px 0 0 83px;
        display: flex;
        justify-content: center;
        min-height: calc(100vh - 80px);
    }
/*----------------------Version téléphone-------------------------------*/
    @media all and (max-width: 768px) {
        .community {
            padding: 80px 0 0 0;
        }
    }
/*----------------------Fin Version téléphone-------------------------------*/
</style>
