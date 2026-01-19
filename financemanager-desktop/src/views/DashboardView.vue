<script setup lang="ts">
import { onMounted, ref } from "vue";
import { useRouter } from "vue-router";
import { authApi, tokenStorage } from "../api/auth";

const router = useRouter();
const user = ref<any>(null);
const isLoading = ref(true);

onMounted(async () => {
  try {
    user.value = await authApi.getMe();
  } catch (error) {
    tokenStorage.clearTokens();
    router.push("/login");
  } finally {
    isLoading.value = false;
  }
});

const handleLogout = async () => {
  try {
    const refreshToken = tokenStorage.getRefreshToken();
    if (refreshToken) await authApi.logout(refreshToken);
  } catch (error) {
    console.error("Logout error:", error);
  } finally {
    tokenStorage.clearTokens();
    router.push("/login");
  }
};
</script>

<template>
  <div class="dashboard">
    <header class="dashboard-header">
      <div class="dashboard-header-content">
        <div class="dashboard-logo">
          <svg fill="none" viewBox="0 0 48 48">
            <path
              d="M4 4H17.3334V17.3334H30.6666V30.6666H44V44H4V4Z"
              fill="currentColor"
            />
          </svg>
          <h1>FinanceManager Pro</h1>
        </div>
        <button class="btn-logout" @click="handleLogout">Déconnexion</button>
      </div>
    </header>
    <main class="dashboard-main">
      <div class="dashboard-content">
        <h2 class="dashboard-title">Tableau de bord</h2>
        <div v-if="isLoading" class="dashboard-subtitle">Chargement...</div>
        <div v-else>
          <p class="dashboard-subtitle">
            Bienvenue {{ user?.email || "utilisateur" }} !
          </p>
          <div class="dashboard-cards">
            <div class="dashboard-card">
              <h3>Trésorerie</h3>
              <p class="dashboard-card-value gold">€ 0.00</p>
            </div>
            <div class="dashboard-card">
              <h3>Factures en attente</h3>
              <p class="dashboard-card-value">0</p>
            </div>
            <div class="dashboard-card">
              <h3>Transactions</h3>
              <p class="dashboard-card-value">0</p>
            </div>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>
