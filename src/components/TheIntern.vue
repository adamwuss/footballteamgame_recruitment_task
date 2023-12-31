<template>
  <div class="the-intern">
    <div class="the-intern__inputs-wrapper">
      <div class="the-intern__inputs">
        <label class="the-intern__label">
          <span class="the-intern__text">First Name</span>
          <input class="the-intern__input" type="text" v-model="firstName" />
          <span v-if="firstNameError" class="the-intern__error">{{ firstNameError }}</span>
        </label>
        <label class="the-intern__label">
          <span class="the-intern__text">Last Name</span>
          <input class="the-intern__input" type="text" v-model="lastName" />
          <span v-if="lastNameError" class="the-intern__error">{{ lastNameError }}</span>
        </label>
      </div>

      <div class="the-intern__buttons">
        <TheButton @click="$emit('cancel')" color="red">
          Cancel
        </TheButton>
        <TheButton @click="handleClick">
          {{ editingIntern ? 'Update Details' : 'Add User' }}
        </TheButton>
      </div>
    </div>

    <div class="the-intern__photo">
      <img :src="local_image || avatar || require('../assets/img/default-user.png')" width="100" height="100" alt="user">
      <label class="the-intern__button">
        <input type="file" accept="image/*" @input="handleImage" />
        <TheCamera />
        Change Photo
      </label>
    </div>
  </div>
</template>

<script>
import TheButton from "@/components/TheButton.vue";
import TheCamera from "@/components/icons/TheCamera.vue";
export default {
  name: "TheIntern",
  components: {
    TheButton,
    TheCamera,
  },
  props: {
    editingIntern: {
      type: Object,
      default: () => ({})
    },
  },
  data() {
    return {
      firstName: this.editingIntern.first_name || '',
      lastName: this.editingIntern.last_name || '',
      firstNameError: "",
      lastNameError: "",
      local_image: this.editingIntern.local_image || '',
      avatar: this.editingIntern.avatar || '',
    };
  },
  mounted() {
    window.addEventListener('keyup', this.handleKey)
  },
  beforeUnmount() {
    window.removeEventListener('keyup', this.handleKey)
  },
  methods: {
    validateFirstName() {
      if (!this.firstName) {
        this.firstNameError = "First Name is required";
      } else if (!/^[a-zA-Z]+$/.test(this.firstName)) {
        this.firstNameError = "First Name should only contain letters";
      } else {
        this.firstNameError = "";
      }
    },
    validateLastName() {
      if (!this.lastName) {
        this.lastNameError = "Last Name is required";
      } else if (!/^[a-zA-Z]+$/.test(this.lastName)) {
        this.lastNameError = "Last Name should only contain letters";
      } else {
        this.lastNameError = "";
      }
    },
    handleImage(event)  {
      if (!event.target?.files) {
        return;
      }

      const file = event.target?.files[0];

      if (file && file.type.startsWith('image/')) {
        this.loadImage(file);
      }
    },
    loadImage(file) {
      const reader = new FileReader();
      reader.onload = (event) => {
        this.local_image = event.target.result;
      };
      reader.readAsDataURL(file);
    },
    validation() {
      this.validateFirstName();
      this.validateLastName();
    },
    async handleClick() {
      this.firstName = this.firstName.trim();
      this.lastName = this.lastName.trim();

      this.validation()

      if (this.firstNameError || this.lastNameError) {
        return; // Don't proceed if there are validation errors
      }

      this.$emit("close", {
        first_name: this.firstName,
        last_name: this.lastName,
        avatar: this.local_image || this.editingIntern.avatar,
        local_image: this.local_image || this.editingIntern.avatar,
        id: this.editingIntern.id || Date.now(),
      });
    },
    handleKey(event) {
      if (event.key === 'Escape') {
        this.$emit('cancel')
      }

      else if (event.key === 'Enter') {
        this.handleClick()
      }
    }
  },
}
</script>

<style scoped lang="scss">
.the-intern {
  display: flex;
  justify-content: space-between;
  flex-direction: column;

  padding-top: 50px;
  width: 100%;

  @media screen and (min-width: 1024px) {
    height: 300px;
    flex-direction: row;
  }

  &__inputs-wrapper {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    margin-bottom: 30px;
  }

  &__inputs {
    display: flex;
    flex-direction: column;
    gap: 30px;
    margin-bottom: 30px;

    @media screen and (min-width: 1024px) {
      flex-direction: row;
      width: 400px;
    }
  }

  &__label {
    display: flex;
    flex-direction: column;
    gap: 10px;
  }

  &__text {
    font-weight: 700;
  }

  &__input {
    width: 100%;
    height: 25px;

    @media screen and (min-width: 480px) {
      width: 300px;
    }
  }

  &__error {
    color: red;
  }

  &__submit {
    width: 150px;
    height: 50px;
    padding: 5px 10px;
    background: green;
  }

  &__photo {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: space-between;
    width: 100%;
    padding: 20px;
    gap: 20px;

    @media screen and (min-width: 480px) {
      width: 400px;
    }

    img {
      border-radius: 50%;
    }
  }

  &__button {
    width: 100%;
    height: 35px;
    cursor: pointer;
    display: flex;
    justify-content: center;
    align-items: center;
    border: 1px solid grey;
    border-radius: 5px;
    color: #3a3a3a;
    gap: 10px;

    svg {
      width: 15px;
    }

    input {
      display: none;
    }
  }

  &__buttons {
    display: flex;
    gap: 10px;
    width: 100%;
    justify-content: space-between;

    @media screen and (min-width: 480px) {
      justify-content: start;
    }
  }
}
</style>
