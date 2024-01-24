<template>
  <v-app id="inspire">
    <v-navigation-drawer v-model="drawer"
    
    app
    color="#757575"
    >
      <!--  -->

      <template v-slot:prepend>
        <!-- <v-avatar>
          <v-img
            src="/avatar.jpg"
            alt="John"
          ></v-img>
        </v-avatar> -->
        <NagumoLogo />
        <v-divider></v-divider>
        <v-icon size="50px" class="icone-custom">mdi-account-circle
          
        </v-icon> 
        <strong><span class="texto-custom">cherifbekopanda@gmail.com</span></strong>
        <v-spacer></v-spacer>
        
      </template>

        <v-divider></v-divider>

        <v-list density="compact" nav>
          <v-list-item >

            <nuxt-link to="/">
              
              <v-list-item-action>
                <v-icon size="20px" class="icone-custom">mdi-home</v-icon> 
              </v-list-item-action>
              <span class="texto-custom">Inicio</span>
            </nuxt-link>   
            
           </v-list-item>

           <v-divider></v-divider>

          <v-list-item >
            
            <nuxt-link to="/listatarefa">
              <v-list-item-action>
                <v-icon size="20px" class="icone-custom">mdi-clipboard-list</v-icon>
              </v-list-item-action>
              <span class="texto-custom no-text-decoration"> Criar a tarefa</span>
            </nuxt-link>
            
          </v-list-item>

          <v-divider></v-divider>

          <v-list-item >
            <nuxt-link to="/listar-produtos">
            <v-list-item-action>
              <v-icon size="20px" class="icone-custom">mdi-account-group-outline</v-icon>
            </v-list-item-action>
              <span class="texto-custom"> Usuarios</span>
            </nuxt-link>
          </v-list-item>

           <v-divider></v-divider>

            <v-list-item >
                
              <nuxt-link to="/listar-produtos">
                <v-list-item-action>
                  <v-icon size="20px" class="icone-custom">mdi-list-box-outline</v-icon>
                </v-list-item-action>
                <span class="texto-custom">Produtos</span>
              </nuxt-link>

            </v-list-item>

            <v-divider></v-divider>

            <v-list-item >
              <nuxt-link to="/cadastro_produto">
                <v-list-item-action>
                  <v-icon size="20px" class="icone-custom">mdi-view-grid-plus</v-icon>
                </v-list-item-action>
                <span class="texto-custom">Criar Prod</span>
              </nuxt-link>
         
            </v-list-item>
            <v-divider></v-divider>
            <v-spacer></v-spacer>

            <!-- Botão de logout -->
           <v-btn @click="logout" class="logout-button" outlined>
            Sair
          </v-btn>


          </v-list>
    </v-navigation-drawer>

    <!-- <v-app-bar app>
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>

      <v-app-bar-title>Application</v-app-bar-title>
    </v-app-bar> -->


    <v-app-bar
      app
      color="#1565C0"
        src=""
      >
      

        <v-app-bar-nav-icon @click="drawer = !drawer" class="icone-custom"></v-app-bar-nav-icon>

        <v-app-bar-title>
          <NagumoLogo />
        </v-app-bar-title>

        <v-spacer></v-spacer>

        <v-btn icon :to="{ name: 'index' }">
          <v-icon class="icone-custom">mdi-magnify</v-icon>
        </v-btn>

        <v-btn icon>
          <v-icon class="icone-custom">mdi-heart</v-icon>
        </v-btn>

        <v-btn icon>
          <v-icon class="icone-custom">mdi-dots-vertical</v-icon>
        </v-btn>
      </v-app-bar>
    <v-main>
      <!--  -->
      <v-container>
          <Nuxt />
        </v-container>
    </v-main>
   
   
    <!-- FOOTER -->

    <template>
  <v-footer color="#1565C0" class="d-flex flex-column">
    <div class="bg-teal d-flex w-100 align-center px-4">
      <span class="texto-custom">Hortifruti - Supermercado Nagumo!</span> 

      <v-spacer></v-spacer>
      <v-icon>mdi-instagram</v-icon>
      <v-icon>mdi-facebook</v-icon>
      
    </div>

    <div class="px-4 py-2 bg-black text-center w-100">
        <strong> © By Panda Sherif--{{ new Date().getFullYear() }}</strong>
    </div>
  </v-footer>
</template>

    <!-- <v-footer class="d-flex flex-column">
    <div class="bg-teal d-flex w-100 align-center px-4">
     Hortifruti Supermercado Nagumo!

      <v-spacer></v-spacer>
    </div>

    <div class="px-4 py-2 bg-black text-center w-100">
      {{ new Date().getFullYear() }} — <strong>Email: cherifbekopanda@gmail.com © 2023 <br>Copyright: by Sherif Panda</strong>
    </div>
  </v-footer> -->

  </v-app>
</template>

<script setup>
  import { ref } from 'vue'

  const drawer = ref(null)

  const logout = () => {
  console.log('Usuário deslogado');
  /* router.push('/'); */
};
</script>

<script>
  export default {
    name: 'DefaultLayout',
    data: () => ({ drawer: null,
      
    }),
  }
</script>

<style scoped>
.icone-custom {
  color: white !important; /* !important força a aplicação do estilo */
}
.no-text-decoration {
  text-decoration: none !important;
}
.texto-custom {
  color: white; /* Substitua pela cor desejada para o texto */
  text-decoration: none !important;
}

.logout-button {
  color: white;
  border-color: white;
}

.logout-button:hover {
  color: black;
  background-color: white;
  border-color: white;
}
</style>