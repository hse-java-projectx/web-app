<template>
  <SingleView>
    <template #header>
      <NotFound v-if="error.has" :message="error.message" />
      <span v-if="!storageIsSigned" class="big-font">
        <router-link to="/signin">Sign in</router-link> to edit this profile
      </span>
    </template>
    <template #content>
      <span class="big-font">
        <b> Edit Profile </b>
      </span>
      <div>
        <HorCylon v-if="!canEditRequest.recieved" />
        <div v-else-if="!canEditRequest.canEdit">
          {{ canEditRequest.message }}
        </div>
        <EditTeam
          v-else-if="routeProfileType === 'team'"
          :teamId="routeProfileId"
          @editError="onEditError"
        />
        <EditUser
          v-else-if="routeProfileType === 'user'"
          :userId="routeProfileId"
          @editError="onEditError"
        />
      </div>
    </template>
  </SingleView>
</template>
<script>
import Backend from '@/js/backend/main';
import { mapGetters } from 'vuex';

import SingleView from '@/components/SingleView.vue';
import NotFound from '@/views/NotFound.vue';
import EditTeam from '@/views/edit/EditTeam.vue';
import EditUser from '@/views/edit/EditUser.vue';
import HorCylon from '@/components/animated/HorCylon.vue';

export default {
  components: {
    SingleView,
    NotFound,
    EditTeam,
    EditUser,
    HorCylon,
  },

  methods: {
    onEditError(er) {
      this.error = {
        has: true,
        message: er.toString(),
      };
    },
  },

  data() {
    return {
      routeProfileId: this.$route.params.profileId,
      routeProfileType: this.$route.params.profileType,

      error: {
        has: false,
        message: null,
      },

      canEditRequest: {
        recieved: false,
        canEdit: null,
        message: null,
      },
    };
  },

  computed: {
    ...mapGetters(['storageIsSigned', 'storageUser']),
  },

  created() {
    if (!this.storageIsSigned) {
      this.canEditRequest = { recieved: true, canEdit: false, message: 'You are not signed' };
      return;
    }
    Backend.canEdit(this.storageUser.id, { id: this.routeProfileId, type: this.routeProfileType })
      .then((response) => {
        this.canEditRequest = {
          recieved: true,
          canEdit: response,
          message: "You don't have permission to edit this profile",
        };
      })
      .catch((er) => {
        this.error = {
          has: true,
          message: er.toString(),
        };
      });
  },
};
</script>
