<template>
  <div>
    <UCard v-if="!success">
      <template #header> Sign-in to the Finance Tracker </template>

      <form @submit.prevent="handleLogin">
        <UFormGroup
          label="Email"
          class="mb-4"
          :required="true"
          help="You will receive an email with the confirmation link"
        >
          <UInput type="email" placeholder="Email" required v-model="email" />
        </UFormGroup>
        <UButton
          type="submit"
          variant="solid"
          color="black"
          :loading="pending"
          :disabled="pending"
          >Sign-in</UButton
        >
      </form>
    </UCard>

    <UCard v-else>
      <template #header> Email has been send </template>

      <div class="text-center">
        <p class="mb-4">
          We have sent an email to
          <span class="font-extrabold text-orange-400">{{ email }}</span>
          with a link to sign-in
        </p>
        <p>
          <span class="font-extrabold text-orange-400">Important</span> The link
          will expires in 5 minutes.
        </p>
      </div>
    </UCard>
  </div>
</template>

<script setup>
const success = ref(false);
const email = ref("");
const pending = ref(false);

const { toastSuccess } = useAppToast();
const supabase = useSupabaseClient();

useRedirectIfAuthenticated();

const handleLogin = async () => {
  pending.value = true;

  try {
    const { error } = await supabase.auth.signInWithOtp({
      email: email.value,
      options: {
        emailRedirectTo: "http://localhost:3000/confirm",
      },
    });

    if (error) {
      toastSuccess({
        title: "Error authenticating",
        description: error.message,
      });
    } else {
      success.value = true;
    }
  } catch (error) {
  } finally {
    pending.value = false;
  }
};
</script>
