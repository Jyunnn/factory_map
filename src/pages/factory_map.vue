<template>
  <v-container>
    <v-row>
      <v-col cols="12">
        <v-app-bar flat height="80">
          <v-app-bar-title>Factory Map</v-app-bar-title>
        </v-app-bar>
      </v-col>
    </v-row>
    <v-row>
      <v-col cols="6" style="flex: 0 0 60%; max-width: 60%;">
        <iframe
          allowfullscreen=""
          height="400"
          loading="lazy"
          referrerpolicy="no-referrer-when-downgrade"
          src="https://maps.google.com/maps?q=New%20York&t=&z=13&ie=UTF8&iwloc=&output=embed"
          style="border:0;"
          width="100%"
        />
      </v-col>
      <v-col cols="6" style="flex: 0 0 40%; max-width: 40%;">
        <v-row>
          <v-col cols="6">
            <!-- Right column first half -->
          </v-col>
          <v-col cols="6">
            <!-- Right column second half -->
          </v-col>
        </v-row>
      </v-col>
    </v-row>
  </v-container>
</template>

<script setup>
//
</script>
