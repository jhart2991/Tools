<template>
  <list-view url="/findingtemplates/" :search="$route.query.search">
    <template #title>Finding Templates</template>
    <template #actions v-if="$auth.hasScope('template_editor')">
      <btn-confirm 
        :action="createTemplate" 
        :confirm="false" 
        button-text="Create"
        button-icon="mdi-plus"
        button-color="primary"
      />
      <btn-import :import="performImport" />
    </template>
    <template #item="{item}">
      <v-list-item :to="`/templates/${item.id}/`" nuxt two-line>
        <v-list-item-content>
          <v-list-item-title>
            <cvss-chip :value="item.data.cvss" />
            {{ item.data.title }}
          </v-list-item-title>
          <v-list-item-subtitle>
            <status-chip :value="item.status" />
            <language-chip :value="item.language" />
            <v-chip v-for="tag in item.tags" :key="tag" class="ma-1" small>
              {{ tag }}
            </v-chip>
          </v-list-item-subtitle>
        </v-list-item-content>
      </v-list-item>
    </template>
  </list-view>
</template>

<script>
import { uploadFileHelper } from '~/utils/upload';

export default {
  head: {
    title: 'Templates',
  },
  methods: {
    async createTemplate() {
      const template = await this.$store.dispatch('templates/create', {
        data: {
          title: 'New Finding Template',
        },
      });
      this.$router.push({ path: `/templates/${template.id}` });
    },
    async performImport(file) {
      const templates = await uploadFileHelper(this.$axios, '/findingtemplates/import/', file);
      this.$router.push({ path: `/templates/${templates[0].id}/` });
    },
  }
}
</script>
