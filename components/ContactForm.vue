<template>
  <div class="contact-form">
    <form @submit.prevent="submitContactForm">
      <div
        class="input-box"
        :class="{ invalid: v$.name.$error, valid: !v$.name.$invalid }"
      >
        <label for="#">Name</label>
        <input
          id="text"
          v-model="formData.name"
          type="text"
          placeholder="Enter your name"
          @blur="v$.name.$touch"
        />
        <p v-if="v$.name.$error">
          First name must be at least
          {{ v$.name.minLength.$params.min }} characters long.
        </p>
      </div>
      <div
        class="input-box"
        :class="{ invalid: v$.email.$error, valid: !v$.email.$invalid }"
      >
        <label for="#">Email</label>
        <input
          id="email"
          type="email"
          placeholder="Enter your email"
          v-model="formData.email"
          @blur="v$.email.$touch"
        />
        <p v-if="v$.email.$error">Enter a valid email address.</p>
      </div>
      <div
        class="input-box"
        :class="{
          invalid: v$.phoneNumber.$error,
          valid: !v$.phoneNumber.$invalid,
        }"
      >
        <label for="#">Number</label>
        <input
          id="number"
          v-model="formData.phoneNumber"
          type="number"
          placeholder="Number"
          @blur="v$.phoneNumber.$touch"
        />
        <p v-if="!v$.phoneNumber.required">Input required</p>
        <p v-else-if="v$.phoneNumber.$error">Enter a valid phone number</p>
      </div>
      <div
        class="input-box"
        :class="{ invalid: v$.subject.$error, valid: !v$.subject.$invalid }"
      >
        <label for="#">Subject</label>
        <input
          id="subject"
          v-model="formData.subject"
          type="text"
          placeholder="Subject"
          @blur="v$.subject.$touch"
        />
        <p v-if="v$.subject.$error">
          Subject must be at least
          {{ v$.subject.minLength.$params.min }} characters long.
        </p>
      </div>
      <div
        class="input-box"
        :class="{ invalid: v$.message.$error, valid: !v$.message.$invalid }"
      >
        <label for="#">Message</label>
        <textarea
          name=""
          id=""
          cols="30"
          rows="10"
          v-model="formData.message"
          @blur="v$.message.$touch"
        ></textarea>
        <p v-if="v$.message.$error">
          Message must be at least
          {{ v$.message.minLength.$params.min }} characters long.
        </p>
      </div>
      <button
        class="btn btn-submit"
        type="submit"
        :disabled="v$.$invalid"
        :style="{ cursor: v$.$invalid ? 'not-allowed' : 'pointer' }"
      >
        Submit
      </button>
    </form>
  </div>
</template>

<script setup>
import { useVuelidate } from "@vuelidate/core";
import { email, helpers, minLength, required } from "@vuelidate/validators";
import { computed, reactive } from "vue";
const { regex } = helpers;

const formData = reactive({
  name: "",
  email: "",
  phoneNumber: "",
  subject: "",
  message: "",
});

const rules = computed(() => {
  return {
    name: {
      required,
      minLength: minLength(2),
    },
    email: {
      required,
      email,
    },
    phoneNumber: {
      required,
      validPhoneNumber: regex(
        /^(\+?\d{1,4}[-\s]?)?\(?\d{3}\)?[-\s]?\d{3}[-\s]?\d{4}$/
      ),
    },
    subject: {
      required,
      minLength: minLength(3),
    },
    message: {
      required,
      minLength: minLength(5),
    },
  };
});

const v$ = useVuelidate(rules, formData);

const submitContactForm = () => {
  v$.value.$validate();

  if (!v$.value.$error) {
    const data = {
      name: formData.name,
      email: formData.email,
      phoneNumber: formData.phoneNumber,
      subject: formData.subject,
      message: formData.message,
    };
    console.log("Submitted successfully", data);

    Object.keys(formData).forEach((key) => {
      formData[key] = "";
    });
    v$.value.$reset();
  }
};
</script>

<style lang="scss" scoped>
.invalid input,
.invalid textarea {
  border: 1px solid red !important;
}

.btn {
  cursor: pointer;
  background: transparent;
  border: none;
}

.contact-form {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding-top: 2rem;
  background: #f7f7ff;
  height: 100vh;

  form {
    display: flex;
    flex-direction: column;
    padding: 2.3rem 2rem;
    margin-bottom: 2rem;
    border-radius: 0.5rem;
    background: white;
    filter: drop-shadow(0px 0px 1px rgba(0, 0, 107, 0.1))
      drop-shadow(0px 0px 5px rgba(0, 0, 255, 0.3));

    .input-box {
      p {
        margin-top: 0.5rem;
        color: red;
        font-family: "Poppins", sans-serif;
        font-size: 0.8rem;
      }
      margin-bottom: 1rem;
      label {
        font-family: "Roboto", sans-serif;
        font-weight: 600;
        display: block;
        margin: 0 0 0.2rem 0;
        opacity: 0.8;
        font-size: 0.9rem;
      }
    }

    input,
    textarea {
      border: 1px solid #ccc;
      outline: none;
      width: 330px;
      padding: 0.7rem 0.75rem;
      border-radius: 5px;
    }
  }

  .btn-submit {
    font-family: "Poppins", sans-serif;
    font-size: 0.8rem;
    background: #000061;
    border: 1px solid transparent !important;
    color: white;
    padding: 0.7rem 0.75rem;
    text-align: center;
    width: 100%;
    border-radius: 5px;
    transition: 0.3s ease-in-out;

    &:hover {
      background: white;
      color: black;
      border: 1px solid #333 !important;
    }
  }
}
</style>
