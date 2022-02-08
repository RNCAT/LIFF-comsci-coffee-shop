<template>
  <div></div>
</template>

<script>
export default {
  data() {
    return {
      liffId: process.env.LIFF_ID_REGISTER,
      webhook: process.env.WEBHOOK_API_URI,
    };
  },
  async mounted() {
    await liff.init({ liffId: this.liffId });

    const { userId } = await liff.getProfile();

    this.Logout(userId);
  },

  methods: {
    async Logout(userId) {
      await this.$axios.delete(`${this.webhook}/user/${userId}`);

      if (
        liff.getContext().type !== "none" &&
        liff.getContext().type !== "external"
      ) {
        liff.closeWindow();
      }
    },
  },
};
</script>

<style>
</style>

