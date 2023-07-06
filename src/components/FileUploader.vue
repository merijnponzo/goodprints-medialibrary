<script setup>
import { ref } from 'vue'
import typy from 'typy';
import { useForm } from '@inertiajs/vue3'
import resizeImage from "@/Helpers/imageResizer.js";
import pdfToImage from "@/Helpers/pdfToimage.js";


const form = useForm({
    name: null,
    file: null,
    thumbnail: null,
})


const error = ref(null);

// resize photo client sided
async function handleFile(e) {
    // get extension of file
    const extension = e.target.files[0].name.split('.').pop();
    // if extension is pdf use pdfToImage
    if (extension === 'pdf') {
        const pdf = await pdfToImage(e.target.files[0]);
        form.thumbnail = pdf;
        return;
    } else {
        // if not pdf use resizeImage
        const image = await resizeImage(e.target.files[0]);
        form.thumbnail = image;
    }
}
function submit() {
    form.post(route("dashboard.upload.create"), {
        onSuccess: () => {
            form.thumbnail = null;
        }
    })


}


</script>

<template>
    <div class="w-640 shadow-xl mx-auto p-16 rounded-xl space-y-16">
        {{ error }}
        <form @submit.prevent="submit" class="flex flex-col max-w-800 gap-16">
            <input id="file__input" type="file" class="btn" @input="form.file = $event.target.files[0]"
                @change="handleFile($event)" />
            <progress v-if="form.progress" :value="form.progress.percentage" max="100">
                {{ form.progress.percentage }}%
            </progress>
            <button class="btn" type="submit">Submit</button>
            {{}}
        </form>
        <p>Voorvertoning</p>
        <img id="canvas" class="w-320 h-400 object-contain mx-auto" />
    </div>
</template>