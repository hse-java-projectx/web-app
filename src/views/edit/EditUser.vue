<template>
  <div>
    <HorCylon v-if="!userRequest.recieved" />
    <div v-else>
      <div class="page-item-container">
        <!-- Name -->
        <b-form @submit="onFormSubmit">
          <b-form-group label="Name" label-cols-sm="2">
            <b-form-input
              trim
              v-model="form.name.value"
              placeholder="Enter your name"
              :state="form.name.valid"
              required
            ></b-form-input>
            <b-form-invalid-feedback :state="form.name.valid">
              {{ form.name.feedback }}
            </b-form-invalid-feedback>
          </b-form-group>

          <!-- Avatar -->
          <b-form-group label="Profile avatar" label-cols-sm="2">
            <b-form-file
              v-model="form.avatarPath.value"
              :state="form.avatarPath.valid"
              placeholder="Choose a file or drop it here..."
              drop-placeholder="Drop file here..."
            />
            <b-button variant="outline-danger" class="mt-2" size="sm">
              Delete avatar
            </b-button>
          </b-form-group>

          <!-- Bio -->
          <b-form-group label="Bio" label-cols-sm="2">
            <b-form-input
              trim
              v-model="form.bio.value"
              placeholder="Tell about yourself"
              :state="form.bio.valid"
            ></b-form-input>
            <b-form-invalid-feedback :state="form.bio.valid">
              {{ form.bio.feedback }}
            </b-form-invalid-feedback>
          </b-form-group>

          <!-- Contacts -->
          <div>
            <p>Contact information</p>
            <b-list-group>
              <b-list-group-item
                v-for="(contact, index) in form.contacts.value"
                :key="index"
              >
                <b-row>
                  <b-col cols="auto" class="my-auto pr-0">
                    <b-button
                      variant="outline-danger"
                      size="sm"
                      class="rounded-circle"
                      @click.prevent="onClickRemoveContact(contact.name)"
                    >
                      <b-icon icon="x" scale="1.3" />
                    </b-button>
                  </b-col>
                  <b-col cols="3" md="2" class="my-auto">
                    <span>{{ contact.name }}</span>
                  </b-col>
                  <b-col>
                    <b-input
                      trim
                      v-model="contact.ref"
                      :placeholder="`Enter your ${contact.name}`"
                      size="sm"
                      required
                    />
                  </b-col>
                </b-row>
              </b-list-group-item>
              <b-list-group-item>
                <b-row>
                  <b-col cols="auto" class="my-auto pr-0">
                    <b-button
                      variant="outline-success"
                      size="sm"
                      class="rounded-circle"
                      @click.prevent="onClickAddContact"
                    >
                      <b-icon icon="plus" scale="1.3" />
                    </b-button>
                  </b-col>
                  <b-col cols="auto" class="my-auto pr-0">
                    <b-input
                      trim
                      v-model="form.newContact.value"
                      size="sm"
                      placeholder="Enter new contact name"
                    />
                  </b-col>
                </b-row>
              </b-list-group-item>
            </b-list-group>
            <b-form-invalid-feedback :state="form.contacts.valid">
              {{ form.contacts.feedback }}
            </b-form-invalid-feedback>
          </div>

          <br />

          <b-button type="submit" variant="success"> Save changes </b-button>
          <b-form-valid-feedback :state="form.submitRequest.valid">
            {{ form.submitRequest.feedback }}
          </b-form-valid-feedback>
          <b-form-invalid-feedback :state="form.submitRequest.valid">
            {{ form.submitRequest.feedback }}
          </b-form-invalid-feedback>
        </b-form>
      </div>
    </div>
    <br />
    <span class="big-font">
      <b> Update password </b>
    </span>
    <div class="page-item-container">
      <b-form @submit.prevent="onPasswordSubmit">
        <!-- Old Password -->
        <b-form-group label="Old password" label-cols-sm="2">
          <b-form-input
            trim
            v-model="formPassword.old.value"
            placeholder="Enter your old password"
            :state="formPassword.old.valid"
            required
            type="password"
            autocomplete="password"
          ></b-form-input>
          <b-form-invalid-feedback :state="formPassword.old.valid">
            {{ formPassword.old.feedback }}
          </b-form-invalid-feedback>
        </b-form-group>

        <!-- New Password -->
        <b-form-group label="New password" label-cols-sm="2">
          <b-form-input
            trim
            v-model="formPassword.new.value"
            placeholder="Enter your new password"
            :state="formPassword.new.valid"
            required
            type="password"
            autocomplete="password"
          ></b-form-input>
          <b-form-invalid-feedback :state="formPassword.new.valid">
            {{ formPassword.new.feedback }}
          </b-form-invalid-feedback>
        </b-form-group>

        <b-button type="submit" variant="success"> Update password </b-button>
        <b-form-valid-feedback :state="formPassword.passwordRequest.valid">
          {{ formPassword.passwordRequest.feedback }}
        </b-form-valid-feedback>
        <b-form-invalid-feedback :state="formPassword.passwordRequest.valid">
          {{ formPassword.passwordRequest.feedback }}
        </b-form-invalid-feedback>
      </b-form>
    </div>
  </div>
</template>
<script>
import Backend from '@/js/backend/main';
import { mapGetters } from 'vuex';

import HorCylon from '@/components/animated/HorCylon.vue';

export default {
  components: { HorCylon },

  props: {
    userId: String,
  },

  computed: {
    ...mapGetters(['storageIsSigned', 'storageUser', 'storageAccessToken']),
  },

  data() {
    return {
      form: {
        name: {
          value: null,
          valid: null,
          feedback: null,
        },

        avatarPath: {
          value: null,
          valid: null,
          feedback: null,
          path: null,
        },

        bio: {
          value: null,
          valid: null,
          feedback: null,
        },

        contacts: {
          value: null,
          valid: null,
          feedback: null,
        },

        newContact: {
          value: null,
        },

        submitRequest: {
          recieved: false,
          valid: null,
          feedback: null,
        },
      },

      formPassword: {
        old: {
          value: null,
          valid: null,
          feedback: null,
        },

        new: {
          value: null,
          valid: null,
          feedback: null,
        },

        passwordRequest: {
          valid: null,
          feedback: null,
        },
      },

      userRequest: {
        recieved: false,
        user: null,
      },
    };
  },

  created() {
    Backend.getUser({ type: 'user', id: this.userId })
      .then((user) => {
        this.userRequest = {
          recieved: true,
          user,
        };
        this.form.name.value = user.name;
        this.form.avatarPath.path = user.avatarPath;
        this.form.bio.value = user.bio;
        this.form.contacts.value = [];
        user.contacts.forEach((c) => {
          this.form.contacts.value.push({ ...c });
        });
      })
      .catch((er) => {
        this.$emit('editError', er);
      });
  },

  methods: {
    getFormValidation() {
      return true;
    },

    onFormValid() {
      this.form.submitRequest = {
        recieved: false,
        valid: null,
        feedback: null,
      };
      const submittingContacts = {};
      this.form.contacts.value.forEach((c) => {
        submittingContacts[c.name] = c.ref;
      });
      Backend.editProfile(
        { type: 'user', id: this.userId },
        {
          name: this.form.name.value,
          // avatarPath: this.form.avatarPath.path,
          bio: this.form.bio.value,
          contacts: submittingContacts,
        },
        this.storageAccessToken,
      )
        .then(() => {
          this.form.submitRequest = {
            recieved: true,
            valid: true,
            feedback: 'All changes has been saved',
          };
          setTimeout(() => {
            this.form.submitRequest.feedback = null;
          }, 3000);
        })
        .catch((er) => {
          this.form.submitRequest = {
            recieved: true,
            valid: false,
            feedback: er.toString(),
          };
        });
    },

    onFormSubmit(event) {
      event.preventDefault();
      if (this.getFormValidation()) {
        this.onFormValid();
      }
    },

    onClickRemoveContact(contactName) {
      const index = this.form.contacts.value.indexOf(contactName);
      this.form.contacts.value.splice(index, 1);
    },

    onClickAddContact() {
      if (this.form.newContact.value !== null && this.form.newContact.value.length !== 0) {
        const newContactName = this.form.newContact.value;
        this.form.newContact.value = null;
        this.form.contacts.value.push({ name: newContactName, ref: null });
      }
    },

    onPasswordSubmit() {
      if (this.formPassword.new.value.length < 8) {
        this.formPassword.new.valid = false;
        this.formPassword.new.feedback = 'Password must contain at least 8 symbols';
        return;
      }

      Backend.updatePassword(this.formPassword.old.value,
        this.formPassword.new.value,
        this.storageAccessToken)
        .then(() => {
          this.formPassword.old.valid = true;
          this.formPassword.new.valid = true;
          this.formPassword.passwordRequest.valid = true;
          this.formPassword.passwordRequest.feedback = 'Password was updated successfully';
        })
        .catch((e) => {
          this.formPassword.passwordRequest.valid = false;
          this.formPassword.passwordRequest.feedback = e.toString();
        });
    },
  },
};
</script>
