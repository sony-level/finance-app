<script setup lang="ts">
import { ref } from "vue";
import { useRouter } from "vue-router";
import { authApi, tokenStorage } from "../api/auth";

const router = useRouter();
const email = ref("");
const password = ref("");
const rememberMe = ref(false);
const showPassword = ref(false);
const isLoading = ref(false);
const errorMessage = ref("");

const handleLogin = async () => {
  isLoading.value = true;
  errorMessage.value = "";
  try {
    const response = await authApi.login(email.value, password.value);
    tokenStorage.setTokens(response.access_token, response.refresh_token);
    router.push("/dashboard");
  } catch (error: any) {
    errorMessage.value =
      error.response?.data?.detail ||
      error.response?.data?.error ||
      "Erreur de connexion";
  } finally {
    isLoading.value = false;
  }
};

const handleGoogleLogin = async () => {
  try {
    const response = await authApi.loginWithGoogle();
    if (response.url) window.location.href = response.url;
  } catch (error) {
    errorMessage.value = "Erreur lors de la connexion Google";
  }
};

const goToRegister = () => router.push("/register");
</script>

<template>
  <div class="auth-container">
    <div class="auth-branding">
      <div class="auth-branding-pattern"></div>
      <div class="auth-logo">
        <svg fill="none" viewBox="0 0 48 48">
          <path
            d="M4 4H17.3334V17.3334H30.6666V30.6666H44V44H4V4Z"
            fill="currentColor"
          />
        </svg>
        <h2>FinanceManager</h2>
      </div>
      <div class="auth-quote">
        <div class="auth-quote-mark">"</div>
        <h1>La clarté financière est la boussole de votre croissance.</h1>
        <p>
          Gérez votre trésorerie avec une précision chirurgicale et une sérénité
          absolue.
        </p>
      </div>
      <div class="auth-footer">
        <p>© 2026 FinanceManager Pro</p>
        <span class="auth-footer-dot"></span>
        <div class="auth-footer-secure">
          <svg fill="currentColor" viewBox="0 0 20 20">
            <path
              fill-rule="evenodd"
              d="M2.166 4.999A11.954 11.954 0 0010 1.944 11.954 11.954 0 0017.834 5c.11.65.166 1.32.166 2.001 0 5.225-3.34 9.67-8 11.317C5.34 16.67 2 12.225 2 7c0-.682.057-1.35.166-2.001zm11.541 3.708a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z"
              clip-rule="evenodd"
            />
          </svg>
          <span>Sécurisé par chiffrement AES-256</span>
        </div>
      </div>
      <div class="auth-branding-glow"></div>
    </div>
    <div class="auth-form-panel">
      <div class="auth-form-container">
        <div class="auth-mobile-logo">
          <svg fill="none" viewBox="0 0 48 48">
            <path
              d="M4 4H17.3334V17.3334H30.6666V30.6666H44V44H4V4Z"
              fill="currentColor"
            />
          </svg>
          <h2>FinanceManager</h2>
        </div>
        <div class="auth-header">
          <h2>Bon retour parmi nous</h2>
          <p>
            Veuillez entrer vos identifiants pour accéder à votre espace
            sécurisé.
          </p>
        </div>
        <div v-if="errorMessage" class="error-message">{{ errorMessage }}</div>
        <form class="auth-form" @submit.prevent="handleLogin">
          <div class="form-group">
            <label>Adresse e-mail</label>
            <div class="input-wrapper">
              <svg
                class="input-icon"
                fill="none"
                stroke="currentColor"
                viewBox="0 0 24 24"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M3 8l7.89 5.26a2 2 0 002.22 0L21 8M5 19h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z"
                />
              </svg>
              <input
                v-model="email"
                type="email"
                placeholder="nom@entreprise.com"
                required
              />
            </div>
          </div>
          <div class="form-group">
            <div class="form-group-header">
              <label>Mot de passe</label>
              <a href="#">Mot de passe oublié ?</a>
            </div>
            <div class="input-wrapper has-toggle">
              <svg
                class="input-icon"
                fill="none"
                stroke="currentColor"
                viewBox="0 0 24 24"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z"
                />
              </svg>
              <input
                v-model="password"
                :type="showPassword ? 'text' : 'password'"
                placeholder="••••••••"
                required
              />
              <button
                type="button"
                class="input-toggle"
                @click="showPassword = !showPassword"
              >
                <svg
                  v-if="!showPassword"
                  fill="none"
                  stroke="currentColor"
                  viewBox="0 0 24 24"
                >
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"
                  />
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z"
                  />
                </svg>
                <svg
                  v-else
                  fill="none"
                  stroke="currentColor"
                  viewBox="0 0 24 24"
                >
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M13.875 18.825A10.05 10.05 0 0112 19c-4.478 0-8.268-2.943-9.543-7a9.97 9.97 0 011.563-3.029m5.858.908a3 3 0 114.243 4.243M9.878 9.878l4.242 4.242M9.88 9.88l-3.29-3.29m7.532 7.532l3.29 3.29M3 3l3.59 3.59m0 0A9.953 9.953 0 0112 5c4.478 0 8.268 2.943 9.543 7a10.025 10.025 0 01-4.132 5.411m0 0L21 21"
                  />
                </svg>
              </button>
            </div>
          </div>
          <div class="checkbox-group">
            <input v-model="rememberMe" type="checkbox" id="remember" />
            <label for="remember">Rester connecté pendant 30 jours</label>
          </div>
          <button type="submit" class="btn-primary" :disabled="isLoading">
            <span v-if="isLoading">Chargement...</span>
            <span v-else>Se connecter</span>
          </button>
        </form>
        <div class="divider">
          <div class="divider-line"></div>
          <span class="divider-text">ou continuer avec</span>
          <div class="divider-line"></div>
        </div>
        <div class="social-buttons">
          <button type="button" class="btn-social" @click="handleGoogleLogin">
            <svg viewBox="0 0 24 24">
              <path
                fill="#4285F4"
                d="M22.56 12.25c0-.78-.07-1.53-.2-2.25H12v4.26h5.92c-.26 1.37-1.04 2.53-2.21 3.31v2.77h3.57c2.08-1.92 3.28-4.74 3.28-8.09z"
              />
              <path
                fill="#34A853"
                d="M12 23c2.97 0 5.46-.98 7.28-2.66l-3.57-2.77c-.98.66-2.23 1.06-3.71 1.06-2.86 0-5.29-1.93-6.16-4.53H2.18v2.84C3.99 20.53 7.7 23 12 23z"
              />
              <path
                fill="#FBBC05"
                d="M5.84 14.09c-.22-.66-.35-1.36-.35-2.09s.13-1.43.35-2.09V7.07H2.18C1.43 8.55 1 10.22 1 12s.43 3.45 1.18 4.93l2.85-2.22.81-.62z"
              />
              <path
                fill="#EA4335"
                d="M12 5.38c1.62 0 3.06.56 4.21 1.64l3.15-3.15C17.45 2.09 14.97 1 12 1 7.7 1 3.99 3.47 2.18 7.07l3.66 2.84c.87-2.6 3.3-4.53 6.16-4.53z"
              />
            </svg>
            Google
          </button>
          <button type="button" class="btn-social">
            <svg
              class="icon-sso"
              fill="none"
              stroke="currentColor"
              viewBox="0 0 24 24"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M19 21V5a2 2 0 00-2-2H7a2 2 0 00-2 2v16m14 0h2m-2 0h-5m-9 0H3m2 0h5M9 7h1m-1 4h1m4-4h1m-1 4h1m-5 10v-5a1 1 0 011-1h2a1 1 0 011 1v5m-4 0h4"
              />
            </svg>
            SSO
          </button>
        </div>
        <p class="auth-link">
          Pas encore de compte ?
          <button @click="goToRegister">S'inscrire gratuitement</button>
        </p>
      </div>
    </div>
  </div>
</template>
