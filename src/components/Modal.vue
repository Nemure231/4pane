<script>
export default {
}
</script>

<template>
<div class="fixed z-10 inset-0" aria-labelledby="modal-title" role="dialog" aria-modal="true">
    <div class="flex items-center justify-center min-h-screen pt-4 px-4 pb-20 text-center sm:block sm:p-0">
        <span class="hidden sm:inline-block sm:align-middle sm:h-screen" aria-hidden="true">&#8203;</span>
        <div class="relative inline-block  align-bottom bg-white rounded-lg text-left overflow-hidden shadow-xl transform transition-all sm:my-8 sm:align-middle">
            <div class="bg-white">
                <slot name="modalPane">
                </slot>
            </div>
        </div>
    </div>
</div>



</template>