<template>
  <div>
    <h1>Bienvenue</h1>
    
    <h2>Navigation Côté Client</h2>
    <NuxtLink to="/bieres-client?type=ale" style="color: violet;">Voir les Ales</NuxtLink><br>
    <NuxtLink to="/bieres-client?type=stouts" style="color: violet;">Voir les Stouts</NuxtLink>

    <h2>Navigation Côté Serveur</h2>
    <NuxtLink to="/bieres-serveur?type=ale" style="color: violet;">Voir les Ales</NuxtLink><br>
    <NuxtLink to="/bieres-serveur?type=stouts" style="color: violet;">Voir les Stouts</NuxtLink>
  </div>
</template>

<script setup>
</script>