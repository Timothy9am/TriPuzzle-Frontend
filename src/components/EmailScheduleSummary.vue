<script setup>
import { ref, defineProps } from "vue"
import emailjs from "@emailjs/browser"
import { SendMail, MailOut } from "@iconoir/vue"

const props = defineProps({
  scheduleSummaryText: {
    type: Array,
    required: true,
  },
})

const PUBLIC_KEY = process.env.VITE_EMAILJS_PUBLIC_KEY
const SERVICE_ID = process.env.VITE_EMAILJS_SERVICE_ID
const TEMPLATE_ID = process.env.VITE_EMAILJS_TEMPLATE_ID
const email = ref("")
const emailSendSuccess = ref(null)
const sendSuccessMsg = ref("")
const sendEmail = () => {
  const templateParams = {
    scheduleSummary: props.scheduleSummaryText.join("\n"),
    email: email.value,
  }
  emailjs.send(SERVICE_ID, TEMPLATE_ID, templateParams, PUBLIC_KEY).then(
    (response) => {
      sendSuccessMsg.value = "信件寄送成功!"
      emailSendSuccess.value.showModal()
      setTimeout(() => {
        emailSendSuccess.value.close()
      }, 1500)
    },
    (error) => {
      console.log("信件寄送失敗...", error)
    }
  )
  email.value = ""
}
</script>

<template>
  <button
    class="w-full h-12 px-5 py-3 border border-primary-600 text-primary-600 hover:bg-primary-100 text-center rounded-3xl font-medium"
    onclick="EmailScheduleSummary.showModal()"
  >
    <SendMail class="w-7 h-7 inline-block pe-2" />
    <span>寄送 Email</span>
  </button>
  <dialog id="EmailScheduleSummary" class="modal">
    <div class="modal-box w-screen md:w-[480px] relative">
      <form method="dialog">
        <div class="w-full h-10">
          <button
            class="btn btn-sm btn-circle btn-ghost absolute right-2 top-2"
          >
            ✕
          </button>
        </div>
        <div class="pb-20">
          <div class="pt-4">
            <h3 class="text-2xl font-medium text-center mb-4">寄送 Email</h3>
            <input
              type="text"
              placeholder="輸入要寄送的 Email"
              class="input input-bordered w-full px-6 focus:outline-primary-600"
              v-model="email"
            />
          </div>
          <div
            class="w-full flex gap-3 h-20 px-6 py-4 bg-white border-t border-gray absolute bottom-0 right-0"
          >
            <button
              class="w-full h-12 px-5 py-3 border border-primary-600 text-primary-600 text-center rounded-3xl font-medium hover:bg-primary-100"
            >
              取消
            </button>
            <button
              class="w-full h-12 px-5 py-3 bg-primary-600 hover:bg-primary-700 text-white text-center rounded-3xl font-medium"
              @click="sendEmail"
            >
              寄送
            </button>
          </div>
        </div>
      </form>
    </div>
    <form method="dialog" class="modal-backdrop">
      <button class="btn btn-sm btn-circle btn-ghost absolute right-2 top-2">
        ✕
      </button>
      <button>close</button>
    </form>
  </dialog>
  <!-- schedule summary send success Modal -->
  <dialog ref="emailSendSuccess" class="modal w-[384px] mx-auto">
    <div class="modal-box">
      <form method="dialog"></form>
      <MailOut class="mx-auto w-14 h-14 text-primary-600 mb-3" />
      <h3 class="text-xl font-bold text-center">{{ sendSuccessMsg }}</h3>
    </div>
  </dialog>
</template>
