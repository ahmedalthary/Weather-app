<template>
    <Teleport to=".modals">
        <Transition name="modal-outer">
            <div v-show="modalStatus" class=" w-full h-screen fixed top-0 left-0 bg-black bg-opacity-50">
                <Transition name="modal-inner">
                    <div v-if="modalStatus" class=" container mt-36">
                        <div class="bg-white rounded p-4 text-sm flex flex-col gap-3 text-black">
                            <slot></slot>
                            <button @click="$emit('close-modal')" type="button"
                                class=" mt-5 self-start bg-weather-primary py-1 px-3 rounded text-white">Close</button>
                        </div>

                    </div>
                </Transition>
            </div>
        </Transition>
    </Teleport>
</template>

<script setup>
defineEmits(["close-modal"])

defineProps(["modalStatus"])
</script>
<style scoped>
.modal-outer-enter-active,
.modal-outer-leave-active {
    transition: opacity 0.3s cubic-bezier(0.52, 0.02, 0.19, 1.02);
}

.modal-outer-enter-from,
.modal-outer-leave-to {
    opacity: 0;
}

.modal-inner-enter-active {
    transition: all 0.3s cubic-bezier(0.52, 0.02, 0.19, 1.02) 0.15s;
}

.modal-inner-leave-active {
    transition: all 0.3s cubic-bezier(0.52, 0.02, 0.19, 1.02);
}

.modal-inner-enter-from {
    opacity: 0;
    transform: scale(0.8);
}

.modal-inner-leave-to {
    transform: scale(0.8);
}
</style>